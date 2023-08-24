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
   ```
