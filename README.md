# repo-fullstack
Deploying a FullStack Node.js &amp; Reactjs Application to Kubernetes Using EKS
1. Create working directory
2. In the working directory, mkdir backend and cd into it
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
5. Within the backend folder, create a file and name it app.js, add the following code
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
6. To run the application, use the following command on your server.
   ```
   #This will create the development server to run your application. On your browser, run http://localhost:4000
   npm run dev
   ``` 
