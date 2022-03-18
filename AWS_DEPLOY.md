# Amazon Academy

Amazon Academy is a platform created by Amazon to prepare students to works with amazon Service. 
For our pourposes, well use Amazon Academy Labs to :
    1. Create an EC2 instance
    2. Connect by SSH to our instance and install Docker

## Creating an EC2 Instance
    
   - Log In at [LMS AWS Academy](https://awsacademy.instructure.com/login/canvas) with your student user/passwd
   - At the DashBoard, click on the Lab Course **AWS Academy Learner Lab - Foundation Services [15286]**
   - Now you are inside the AWS Course. The  Module Menu Item show you available course materials : guides, presentations...Click on **Learner Lab - Foundational Services** to go to you lab. Lab image: 
   <p align="center">   
<img width="700" alt="Student Lab" src="https://user-images.githubusercontent.com/29120610/156115436-b32449a0-bb8f-4824-aabf-1590f2904c12.png">
</p>

   - Start the lab by selecting **Start Lab**
   - When the dot next to AWS turns green, your lab environment is ready to use. Click  AWS to launch the AWS Console in a new tab. A new tab will open the AWS Management Console when you click on AWS. The system logged  you into a temporary AWS account and the lab session will automatically end when the session timer expires. The system will save your work when you end the session or the session timer expires. 
   - Go to AWS Console Tab and select services EC2
  <p align="center">   
   <img width="700" alt="AWS Console Page" src="https://user-images.githubusercontent.com/29120610/156117104-6cf633d6-5206-468d-8da6-1021b23351ee.png">
    </p>
   - In the EC2 Service, at the Instances Menu option, we can monitoring our created instances. Click the  **Launch Instance** button to create a new instance
 <p align="center">   
      <img width="700" alt="AWS EC2 Instances" src="https://user-images.githubusercontent.com/29120610/156117432-d11bceb5-6eae-4df6-b956-b5b9e04513c2.png">
</p>
   - Follow wizard steps:
   - Step 1: Choose an Amazon Machine Image  (AMI).
  <p align="center">   
      <img width="700" alt="Choose an Amazon Machine Image " src="https://user-images.githubusercontent.com/29120610/156118584-eb80c36b-e0a8-4d08-87c1-63789d90cdc0.png">
</p>
   - Step 2:Choose Instance Type. We choose medium option.
  <p align="center">   
      <img width="700" alt="Choose Instance Type" src="https://user-images.githubusercontent.com/29120610/156119274-6e00528b-8c4b-4790-83b2-d8440dcd3b44.png">
</p>
   - Step 3:Configure Instance Details- We don't change default values.
   <p align="center">   
      <img width="700" alt="Configure Instance Details" src="https://user-images.githubusercontent.com/29120610/156119663-d8fae02e-1cad-4013-b68a-3af711ef274f.png">
</p>
   - Step 4: Storage Capacity. We increase storage capacity to 64 Gb
   <p align="center">   
      <img width="700" alt="Screenshot 2022-03-01 at 07 34 23" src="https://user-images.githubusercontent.com/29120610/156120023-3f5927c3-f990-4d5f-b70b-71f178905867.png">
</p>
   - Step 5: We dont add any tag.
   - Step 6: Configure Security Group. We open ssh , http, https and 3000 port for all inbound traffic  and every IP.
<p align="center">   
      <img width="700" alt="Screenshot 2022-03-01 at 07 34 23" src="https://user-images.githubusercontent.com/29120610/156120044-d124c94f-2ea7-46cc-9e7e-9e56a987d08b.png">
</p>
    - Step 7: Summary. At the summary step, before launch our instance, a dialog ask us to create a new Key pair or use existing one. We'll create a new one with the default value. That will downlaod the **home.pem** file. We always can create new key pairs at the *AWS Console Menu - In the Security and Network - KeyPair*
<p align="center">   
      <img width="700" alt="Summary Step" src="https://user-images.githubusercontent.com/29120610/156125816-a416b306-cde8-4566-b509-b6e4cbbf9f23.png">
</p>

 - If you will use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it.

  ```
        chmod 400 home.pem
  ```
  
## Connect by ssh 
 
- Once the instace has been created, we need to know its *public ip* and/or *public dns* name. All this information is in the instance detail panel.
 <p align="center">   
      <img width="700" alt="Instance Detail Panel" src="https://user-images.githubusercontent.com/29120610/156125954-61c38152-42c2-4551-8340-8e0422e94051.png">
</p> 
   
 - We'll connect at our EC2 instance with ssh using our key file: 
   ```
   ssh -i home.pem ec2-user@ec2-44-195-26-211.compute-1.amazonaws.com
  ```
  
  - At this point, we recommend create a [new user] (https://aws.amazon.com/premiumsupport/knowledge-center/new-user-accounts-linux-instance/) as docker-deploy and assign a new key pair docker_deploy.pem . Once connected, we'll update server and install docker and docker-compose
 
   ```
      sudo yum update
      sudo yum install docker
      wget https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) 
      sudo mv docker-compose-$(uname -s)-$(uname -m) /usr/local/bin/docker-compose
      sudo chmod -v +x /usr/local/bin/docker-compose
      sudo systemctl enable docker.service
      sudo systemctl start docker.service
  ```
   - Check status with 
   ```
     sudo systemctl status docker.service
   ```

## GitHub Actions
  
Now we have a capable machine of executing Docker containers. Let's configure our project at github to be deployed in our instance at the release creation. 


- Next step is creating secrets to have the information we need. We are going to create three, DEPLOY_HOST, with the IP of the virtual machine; DEPLOY_USER with the user with permissions to access the machine.
<p align="center">   
      <img width="700" alt="Summary Step" src="https://user-images.githubusercontent.com/29120610/156125816-a416b306-cde8-4566-b509-b6e4cbbf9f23.png">
</p>
-Now we are going to create a new docker-compose file called docker-compose-deploy.yaml that will contain the specific docker-compose instructions to deploy the application:
```
version: '3.5'
services:
  restapi:
    image: ghcr.io/cidinene/asw2122_0/restapi:latest
    ports:
      - "5000:5000"
  webapp:
    image: ghcr.io/cidinene/asw2122_0/webapp:latest
    ports:
      - "80:3000"
    depends_on: 
      - restapi
 ```
Note that in this file we are using the images that we uploaded to the github registry instead of building them from scratch.

Now we can configure our actions file to include a new job deploy that will be in charge of deploying this docker-compose file to the virtual machine. It will be executed after pushing the docker images to the registry.

```aws-docker-compose-deploy-steps: 
    name: Deploy over AWS using  SSH 
    runs-on: ubuntu-latest 
    needs: [docker-push-restapi,docker-push-webapp] 
    steps: 
    - name: Docker-Compose Remote Deployment
      uses: fifsky/ssh-action@master
      with: 
        host: ${{ secrets.DEPLOY_HOST }}
        user: ${{ secrets.EXAMPLE_COM_SSH_USER }}
        key: ${{ secrets.EXAMPLE_COM_SSH_PRIVATE_KEY }}
        command: | 
          wget https://raw.githubusercontent.com/cidinene/asw2122_0/master/docker-compose-deploy.yml -O docker-compose.yml 
          docker-compose stop 
          docker-compose rm -f 
          docker-compose pull    
          docker-compose up -d 
   ```
This job , using SSH connection,  retrieves the docker compose file,  stops any running  instance, pulls  new images from registry pushed in a previous step and launch our instances.

## Extra modifications needed
In order for everything to work, we need to make some extra modifications in the project. There are related with the restapi URL and how React works. In the webapp code we have in the src/api/api.ts file the following line:

const apiEndPoint= process.env.REACT_APP_API_URI || 'http://localhost:5000/api'
This means that React will look for an environment variable and if it exists, it will take the apiEndPoint from there, chosing localhost in anyother case. Environment variables in React are picked up in building time and not in execution time. That means we need to pass this variable when we are building the docker image before deploying. For that we need to change the Dockerfile for the webapp and add the following lines before npm run build:
```
ARG API_URI="http://localhost:5000/api"
ENV REACT_APP_API_URI=$API_URI
Now this Dockerfile has an argument (with a default value) that will be create the REACT_APP_API_URI environment variable before building the production release of the webapp. We need to pass this argument in the GitHub Actions file, when building the webapp image, that is in the job docker-push-webapp.

env:
   API_URI: http://${{ secrets.DEPLOY_HOST }}:5000/api
```
Lastly we need to configure CORS accept petitions from all the sources in the restapi. This means changing the cors initialization in restapi/server.ts to:

app.use(cors());

## Creating a new release

Everything is ready now to make the deploy. For that we need to create a new release. That will fire up the deployment process that we have just configured:
 <p align="center">   
      <img width="700" alt="Release Step " src="https://user-images.githubusercontent.com/10683040/155293978-8e77e821-ed21-4f28-abd9-282ae9e5661b.png">
</p>

After the actions process is finished, we can access the application using the IP of our virtual machine in the port 3000. Note that is very simple to modify the application to work in the port 80 instead. We only need to open that port and configure react to use this port instead.

<p align="center">   
      <img width="700" alt="Release Step " src="https://user-images.githubusercontent.com/29120610/156131366-e7f5da4c-04a9-4fdd-acbf-7d7e32a7824a.png">
</p>    
    We can check our release at our instance:
   <p align="center">   
      <img width="700" alt="AWSPage1" src="https://user-images.githubusercontent.com/29120610/156132091-9dda96b4-cd44-47ee-ae6d-c4555a9fd30e.PNG">
</p>
 
    
    
    
