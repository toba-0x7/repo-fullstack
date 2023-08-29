# repo-fullstack
Deploying a FullStack Node.js & Reactjs Application to Kubernetes Using EKS
1. Create working directory
2. In the working directory, mkdir ```backend``` and ```cd``` into it
3. In the backend folder run
   ```
   # To initialize the Node.js project and create the ‘package.json’ to install the project dependencies
   npm init --y
   # To install the dependencies Express, and nodemon to help us create the backend server, monitor the application script,       and detect changes, and errors.
   npm i express
   # This allows you to install packages without requiring administrative privileges
   mkdir ~/.npm-global
   npm config set prefix '~/.npm-global'
   export PATH=~/.npm-global/bin:$PATH
   npm i -g nodemon
   npm install cors
   ```
4. Open the ‘package.json’ file, and add the following script under the “script” section.
   ```
   "dev": "nodemon -L app.js"
   ```
5. In the backend folder, create a file and name it app.js, add the following code
   ```
   const express = require('express')
   const cors = require('cors')
   const app = express()
   app.use(cors())
   app.get('/', (req, res) => {
     res.json([
       {
         "id":"1",
         "title":"Movie Review: The Perk of Being a Wallflower"
       },
       {
         "id":"2",
         "title":"Game Review: Need for Speed"
       },
       {
         "id":"3",
         "title":"Show Review: Looking for Alaska"
       }
     ])
   })
   app.listen(4000, () => {
     console.log('listening for requests on port 4000')
   })
   ```
6. To run the application, run this
   ```
   #This will create the development server to run your application. On your browser, run http://private-IP:4000
   npm run dev
   ``` 
### Creating the React.js Application
1. After building your backend server, change the directory out of the backend folder, and create a React.js project using the following command
   ```
   npx create-react-app frontend
   ```
2. We’d use this command to create a new folder named “frontend” in the working directory. In the ‘frontend’ folder, navigate to the “src” folder, and open “App.js”. Delete the existing content and paste the following code.
   ```
   import { useEffect, useState } from 'react'
   import './App.css';
   
   function App() {
     const [blogs, setBlogs] = useState([])
     useEffect(() => {
       fetch('http://privateIP:4000/')
         .then(res => res.json())
         .then(data => setBlogs(data))
     }, [])
   
     return (
       <div className="App">
         <header className="App-header">
           <h1>all blogs</h1>
           {blogs && blogs.map(blog => (
             <div key={blog.id}>{blog.title}</div>
           ))}
         </header>
       </div>
     );
   }
   
   export default App;
   ```
3. Run your application using the following code.
   ```
   npm start
   ```
### Dockerizing the application
1. Navigate to the ‘backend’ directory and create a Dockerfile
   ```
   FROM node:18-alpine
   # The base image for Dockerizing the Node.js application
   
   RUN npm install -g nodemon
   # Installing the nodemon package for monitoring the Express server
   
   WORKDIR /app
   # Creating the working directory
   
   COPY package.json .
   # Copies the dependencies in the package.json file
   
   RUN npm install
   # Installing all the dependencies
   
   COPY . .
   # Copies all the files to the working directory
   
   EXPOSE 4000
   # Container will run on this port
   
   CMD ["npm", "run", "dev"]
   # Start the Docker container Node.js application
   ```
2. To build the image within the backend folder on your server, run the following command.
   ```
   docker build -t <your_dockerhub_tag>/node-js .
   ```
3. To push the newly created Docker Image into Docker Hub.
   ```
   docker login
   # This command is to log into Dockerhub on your local machine
   
   docker push <your_dockerhub_tag>/node-js
   # This command helps you push your application to your Docker Hub account
   ```
### Dockerizing the React application
1. Navigate to the ‘front end’ and create a Dockerfile in the folder. Open the “Dockerfile” and add the following code.
   ```
   FROM node:18-alpine

   WORKDIR /app
   
   COPY package.json .
   
   RUN npm install
   
   COPY . .
   
   EXPOSE 3000
   
   CMD ["npm", "start"]
   ```
2. To build the docker image for deployment.
   ```
   docker build -t <your_docker_hub_tag>/react-js .
   ```
3. After the build process is complete, push the image into the Docker Hub
   ```
   docker push <your_dockerhub_tag>/node-js
   # This command helps you push your application to your Docker Hub account
   ```
### Deploying on Kubernetes
### Prereq
- AWSCLI
- EKSCTL
1. To create a Kubernetes cluster on AWS
   ```
   eksctl create cluster --name cluster --version 1.27 --region us-east-1 --nodegroup-name worker-nodes --node-type t2.medium --nodes 2
   ```
2. After the Kubernetes cluster has been provisioned, check the cluster by running
   ```
   kubectl get nodes
   ```
3. Create a Kubernetes YAML manifest file called ```node-deployment.yaml``` to deploy the two sides of the application. The YAML file will look like the following.
   ```
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: node-js-deployment
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: node-js
     template:
       metadata:
         labels:
           app: node-js
       spec:
         containers:
         - name: node-js
           image: toba0x7/node-js
           resources:
             limits:
               memory: "356Mi"
               cpu: "500m"
           ports:
           - containerPort: 4000
   
   ---
   
   apiVersion: v1
   kind: Service
   metadata:
     name: node-js-service
   spec:
     type: LoadBalancer
     selector:
       app: node-js
     ports:
     - port: 4000
       targetPort: 4000
       protocol: TCP
   ```
3. Create a Kubernetes YAML manifest called ```react-deployment.yaml``` to deploy the front end of the application.
   ```
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: react-js-deployment
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: react-js
     template:
       metadata:
         labels:
           app: react-js
       spec:
         containers:
         - name: react-js
           image: toba0x7/react-js
           resources:
             limits:
               memory: "356Mi"
               cpu: "500m"
           ports:
           - containerPort: 3000
   
   ---
   
   apiVersion: v1
   kind: Service
   metadata:
     name: react-js-service
   spec:
     type: LoadBalancer
     selector:
       app: react-js
     ports:
     - protocol: TCP
       port: 3000
       targetPort: 3000
   ```
4. To apply the two configurations on the Kubernetes cluster
   ```
   kubectl apply -f node-deployment.yaml
   kubectl apply -f react-deployment.yaml
   ```
5. To view all running resources
   ```
   kubectl get all
   ```
6. Copy the load balancer addresses for the react (frontend) and node (backend) application, and paste in your browser.
