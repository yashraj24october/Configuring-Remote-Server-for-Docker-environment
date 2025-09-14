# Guide to configure remote server for deploying dockerized application

<h4>Steps required to get started with docker on remote server:</h4>
<ul>
  <p>Two tools we required to run dockerized applications:</p>
  <li>Docker engine</li>
  <li>Docker compose</li>
</ul>
<br/>
<h3>Installing Docker engine and Docker compose</h3>
<p>Let we have SSH access so we will use CMD to install these tools.</p>
<h5>For Ubuntu based system, Steps:</h5>
<ol>
  <li>Update package list:<br/>
<code>sudo apt-get update</code>
</li>
  <li>
    Install prerequisite packages to allow apt to use packages over HTTPS:<br/>
    <code>sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release</code>
  </li>
  <li>Add Docker's official GPG key:<br/>
  <code>sudo mkdir -p /etc/apt/keyrings</code>
  <code>curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg</code>
</li>
  <li>Set up the Docker repository:<br/>
    <code>echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null</code>
</li>
  <li>Install Docker Engine and related tools:<br/>
<code>sudo apt-get update</code>
    <code>sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin</code>
    <p>This command installs Docker Engine, the Docker CLI, and importantly, Docker Compose as a plugin. This is the modern, recommended way to install Docker Compose.</p>
</li>
  <li>Add user to the docker group (optional but highly recommended):<br/>
    <p>This allows you to run Docker commands without using sudo.

</p>
  <code>sudo usermod -aG docker $USER</code>
</li>
<p>After this, We'll need to log out and log back in to your SSH session for this change to take effect.</p>
</ol>

<h5>Verification of Docker Installation</h5>
<ul>
  <li>Check the Docker version:<br/>
  <code>docker --version</code>
</li>
  <li>Check the Docker Compose version:<br/>
  <code>docker compose version</code>
  </li>
</ul>

<h4>After all these, Now remote server is ready to run our containerized Drupal application!</h4><br/>

<h3>Next Steps:</h3>
<h5>Step1: Now we need to move our application to server using git clone(if repo exist of application) or ftp in a directory with dockerFile and docker-compose.yml</h5><br/>
<h5>Step2: We need to prepare our production environment</h5>
<ul>
<li>Create a seprate docker-compose.yml for production</li>
  <li>Create a .env file for storing all environment variable of application</li>
  <li>Import database on server</li>
</ul>
<br/>
<h5> Now, build and Run your container</h5>
<ul>
  <li>Navigate to project directory where docker-compose.yml exists.</li>
  <li>Run this command to build and start the container: <br/>
<code>docker compose up -d --build</code>.<br/>Docker Compose will read docker-compose.yml file, build the necessary images, and then create and start all the services defined (e.g., web server, database, etc.).</li>
<li>Verify that containers are running:
<code>docker ps</code><br/>
  This command will list all the running containers and their status. We will see entries for your application, database, and any other services we have defined.
</li>
</ul>
<h4>Now,Mapping server port and container port to make Site accessible publicly: <br/>This is done in your docker-compose.yml file under the ports section of your web service.</h4>
The syntax is HOST_PORT:CONTAINER_PORT.<br/>
<ul>
<li>CONTAINER_PORT is the port your application is listening on inside the container (e.g., a Drupal container might listen on port 80 or 443).</li>
  <li>HOST_PORT is the port on your server that you want to expose to the public internet.</li>
  <code>Eg: docker-compose.yml: 
services:
  web:
    image: my-drupal-image
    ports:
      - "80:80"  # This maps the server's port 80 to the container's port 80
      - "443:443" # This maps the server's port 443 to the container's port 443</code>
<p>By doing this, any traffic that hits your server on port 80 will be forwarded to your Drupal container on port 80.
</p>
<li><strong>Final Step: </strong><br/>The final step is to tell the internet where your application is located.<br/><br/>
<ul>
<li>You need to go to your domain name registrar (e.g., GoDaddy, Namecheap) or your DNS provider and create an A record.</li>
<li>This A record will map your chosen domain (e.g., your-drupal-site.com) or subdomain (e.g., www.your-drupal-site.com) to your server's public IP address.</li>
  <h5>Once we've done this, the DNS changes will propagate across the internet. When a user types our domain name into their browser, it will resolve to our server's IP address, hit the mapped port, and be forwarded to our running Docker container.</h5>
</ul>
</li>
<h3>Done! Our application is now running and publicly accessible on the user!</h3>
