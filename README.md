# Dockerize Your VSTS Agents

#### 1. Create a VSTS account with a Personal Access Token
If you do not already have a VSTS account, head to https://www.visualstudio.com/team-services/ to create a free one. Now navigate to **https://\<youraccountname\>.visualstudio.com/_details/security/tokens**. Add a new Personal Access Token that has an Authorized Scope of **Agent Pools (read, manage)**. Make sure to save off the token value, you will need it later.


#### 2. Create an Ubuntu VM
For the purposes of this exercise, I will be using an Azure Free Subscription to create a VM of the type **Ubuntu Server 16.04 LTS**. Select **Password** as your authentication type. When selecting the size of the VM, I will be selecting the 4GB SSD/1600 IOPS option. Make sure **SSH** is selected under Public Inbound Ports.


#### 3. Install Docker on your VM
If you used Azure, select the option to connect to your new VM. Paste the SSH command provided into a command prompt to connect to your VM. To install Docker, run the following command:

`sudo apt-get install docker.io`

Running the command `docker version` you will see that Docker is installed but there is a permission error connecting to the Docker daemon socket. To fix this you need to add the current user to the docker group:

`sudo usermod -aG docker $USER`

Now **restart your VM** and you should be good to go.


#### 4. Create your VSTS build agent in a Docker container
The list of VSTS Agent dockerfiles can be found at https://hub.docker.com/r/microsoft/vsts-agent/. The Ubuntu-standard dockerfiles that are listed contain images for handling many built-in VSTS build and release tasks, such as .NET Core. I will be using ubuntu-16.04-standard so to create my agent I will run the command: 

`docker run -e VSTS_ACCOUNT=<youraccountname> -e VSTS_TOKEN=<your-account-Private-access-token> -e VSTS_WORK=/var/vsts -v /var/run/docker.sock:/var/run/docker.sock -v /var/vsts:/var/vsts -d microsoft/vsts-agent:ubuntu-16.04-standard`

Repeating this command will create additional agents in their own isolated containers.

**Congratulations!** You can now run your builds with your containerized VSTS agent.
