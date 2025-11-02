# Day 8 :  Deploy a Dockerized Web Application on the Cloud.

## 1. Overview.
Running websites can be difficult with all the overhead of creating and managing Virtual Machine (VM) instances, clusters, Pods, services, and more. That's fine for larger, multi-tiered apps, but if you're only trying to get your website deployed and visible, then it's a lot of overhead.

With Cloud Run, the Google Cloud implementation of Knative, you can manage and deploy your website without any of the overhead that you need for VM- or Kubernetes-based deployments. Not only is that a simpler approach from a management perspective, but it also gives you the ability to scale to zero when there are no requests coming to your website.

## 2. Setup and requirements
Self-paced environment setup
Sign-in to the Google Cloud Console and create a new project or reuse an existing one. If you don't already have a Gmail or Google Workspace account, you must create one.

![image_1](https://codelabs.developers.google.com/static/codelabs/cloud-app-engine-python3/img/fbef9caa1602edd0_1440.png)

![image_2](https://codelabs.developers.google.com/static/codelabs/cloud-app-engine-python3/img/a99b7ace416376c4_1440.png)

![image_3](https://codelabs.developers.google.com/static/codelabs/cloud-app-engine-python3/img/5e3ff691252acf41_1440.png)

* The Project name is the display name for this project's participants. It is a character string not used by Google APIs. You can always update it.
* The Project ID is unique across all Google Cloud projects and is immutable (cannot be changed after it has been set). The Cloud Console auto-generates a unique string; usually you don't care what it is. In most codelabs, you'll need to reference your Project ID (typically identified as PROJECT_ID). If you don't like the generated ID, you might generate another random one. Alternatively, you can try your own, and see if it's available. It can't be changed after this step and remains for the duration of the project.

        Caution: A project ID is globally unique and can't be used by anyone else after you've selected it. You are the only user of that ID. Even if a project is deleted, the ID can't be used again

        Note: If you use a Gmail account, you can leave the default location set to No organization. If you use a Google Workspace account, choose a location that makes sense for your organization.

#### Enable the Cloud Run API

Click Navigation menu ☰ > APIs & Services > Dashboard > Enable APIs And Services. .

![above1](https://codelabs.developers.google.com/static/codelabs/cloud-run-deploy/img/5dbb2e6e27a55fcf_1440.png)

Search for "Cloud Run API," then click Cloud Run API > Enable.

![above2](https://codelabs.developers.google.com/static/codelabs/cloud-run-deploy/img/f1fd486174a744cf_1440.png)

### Start Cloud Shell
While Google Cloud can be operated remotely from your laptop, in this codelab you will be using Cloud Shell, a command line environment running in the Cloud.

#### Activate Cloud Shell

1. From the Cloud Console, click Activate Cloud Shell.

![image_4](https://codelabs.developers.google.com/static/codelabs/cloud-app-engine-python3/img/3c1dabeca90e44e5_1440.png)

If this is your first time starting Cloud Shell, you're presented with an intermediate screen describing what it is. If you were presented with an intermediate screen, click Continue.

![image_5](https://codelabs.developers.google.com/static/codelabs/cloud-app-engine-python3/img/9c92662c6a846a5c_1440.png)

It should only take a few moments to provision and connect to Cloud Shell.

![image_6](https://codelabs.developers.google.com/static/codelabs/cloud-app-engine-python3/img/9f0e51b578fecce5_1440.png)

This virtual machine is loaded with all the development tools needed. It offers a persistent 5 GB home directory and runs in Google Cloud, greatly enhancing network performance and authentication. Much, if not all, of your work in this codelab can be done with a browser.

Once connected to Cloud Shell, you should see that you are authenticated and that the project is set to your project ID.

2. Run the following command in Cloud Shell to confirm that you are authenticated:

        gcloud auth list

Command output


    Credentialed Accounts
    ACTIVE  ACCOUNT
    *       <my_account>@<my_domain.com>

    To set the active account, run:
    $ gcloud config set account `ACCOUNT`


Note: The gcloud command-line tool is the powerful and unified command-line tool in Google Cloud. It comes preinstalled in Cloud Shell. You will notice its support for tab completion. You may be prompted to authenticate the first time you run a command. For more information, see gcloud command-line tool overview.

3. Run the following command in Cloud Shell to confirm that the gcloud command knows about your project:

        gcloud config list project

Command output


    [core]
    project = <PROJECT_ID>

If it is not, you can set it with this command:

    gcloud config set project <PROJECT_ID>

Command output

    Updated property [core/project].

4. Set your Project ID:

       gcloud config set project "PROJECT_ID"

**Note**:
This configures the gcloud CLI to use your project.

5. Enable Artifact Registry API

       gcloud services enable artifactregistry.googleapis.com

**Note**:
This command enables the Artifact Registry API for your project, allowing you to create and manage repositories.

6. Create an Artifact Registry Repository in region: REGION

       gcloud artifacts repositories create my-docker-repo \
       --repository-format=docker \
       --location="REGION" \
       --description="My Docker image repository"

**Note**:
Creates a Docker repository in Artifact Registry named `my-docker-repo`.

7. Configure Docker to authenticate with Artifact Registry:

       gcloud auth configure-docker "REGION"-docker.pkg.dev

**Note**:
Authenticates Docker with Artifact Registry for the specified region. This allows you to push and pull images.

## 3. Write the web app
After Cloud Shell launches, you can use the command line to invoke the Cloud SDK gcloud command or other tools available on the virtual machine instance. You can use your $HOME directory in persistent disk storage to store files across projects and between Cloud Shell sessions. Your $HOME directory is private to you and cannot be accessed by other users.

Let's get started by creating a new folder in your $HOME directory for the application:


    mkdir ~/helloworld
    cd ~/helloworld

Create a file named main.py:

    touch main.py

Edit the file with your preferred command line editor (nano, vim, or emacs) or by clicking the Cloud Shell Editor button:

![image_6](https://codelabs.developers.google.com/static/codelabs/cloud-app-engine-python3/img/10af7b1a6240e9f4.gif)

To directly edit the file with Cloud Shell Editor, use this command:

    cloudshell edit main.py

main.py

    from flask import Flask
    app = Flask(__name__)

    @app.route('/')
    def home():
    return "Hello from my Dockerized Cloud App!"

    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=8080)


This web app is a simple web service responding to HTTP GET requests with the message "Hello from my Dockerized Cloud App!".

## 4. Define the dependencies

To specify the dependencies of your web app, go back to the terminal and create a requirements.txt file in the root directory of your project, with the exact version of Flask to use:

    touch requirements.txt

To edit the file with Cloud Shell Editor, use this command:

    cloudshell edit requirements.txt

requirements.txt

    # https://pypi.org/project/Flask
    Flask==3.0.2

The main dependency in your web app is Flask.

## 5.  Create Docker container with Cloud Build
1. Create a Dockerfile:

       FROM python:3.9-slim-buster
       WORKDIR /app
       COPY requirements.txt .
       RUN pip install --no-cache-dir -r requirements.txt
       COPY . .
       EXPOSE 8080
       CMD ["python", "app.py"]

    Note:
    Defines the steps to build your Docker image. It uses a Python base image, installs dependencies, copies the application code, and specifies the command to run the application.

2. Build the Docker image. Replace REGION and PROJECT_ID

       docker build -t "REGION"-docker.pkg.dev/"PROJECT_ID"/my-docker-repo/hello-docker:latest .

    Note:
    Builds the Docker image using the `Dockerfile` in the current directory. It tags the image with the Artifact Registry repository URL.

## 6. Running and Testing the Docker Container

In this task, you will run the Docker image you built and test it to ensure it's working correctly.

1. Run the Docker container:

       docker run -d -p 8080:8080 "REGION"-docker.pkg.dev/"PROJECT_ID"/my-docker-repo/hello-docker:latest

    Note:
    Runs the Docker image in detached mode (`-d`) and maps port 8080 on the host to port 8080 in the container. You may need to configure firewall rules to allow external traffic on port 8080.

2. Check if the container is running:

       docker ps

Note:
Lists the currently running Docker containers.

3. Test the application. Replace PROJECT_ID

        curl http://localhost:8080

    Note:
    Sends an HTTP request to the application running in the container. You should see 'Hello, Docker!' in the output.

4. Stop the Docker container:

       docker stop $(docker ps -q)

    Note:
    Stops all running Docker containers. `docker ps -q` returns only the container IDs.

## 7. Pushing the Image to Artifact Registry

Now that you have a working image, you will push it to your Artifact Registry repository. 

1. Push the Docker image. Replace REGION and PROJECT_ID

       docker push "REGION"-docker.pkg.dev/"PROJECT_ID"/my-docker-repo/hello-docker:latest

    Note:
    Pushes the Docker image to the Artifact Registry repository. This makes the image available for others to use.

8.  Deploy container to Cloud Run

Now that you containerized your website and pushed it to Container Registry, it's time to deploy to Cloud Run!

There are two approaches for deploying to Cloud Run:

Cloud Run (fully managed) is the PaaS model where the entire container lifecycle is managed. You'll use that approach for this codelab.
Cloud Run for Anthos is Cloud Run with an additional layer of control, which allows you to bring your clusters and Pods from GKE. For more information, see Setting up Cloud Run for Anthos on Google Cloud.
Command-line examples will be in Cloud Shell using the environment variables that you set up earlier.

### Command line
Run the following command to deploy your app:

    gcloud run deploy --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:1.0.0 --platform managed 

You'll be asked to specify which region you'd like to run in. Select the region closest to you, then accept the default suggested service name (monolith).

![down2](https://codelabs.developers.google.com/static/codelabs/cloud-run-deploy/img/d52d9419c5166674_1440.png)

For testing purposes, allow unauthenticated requests to the app. Enter y at the prompt.

![dowm1](https://codelabs.developers.google.com/static/codelabs/cloud-run-deploy/img/3a57b32f133dad61_1440.png)

### Verify deployment
To verify that the deployment was created successfully, run the following command. It may take a few moments for the Pod status to be Running:

    gcloud run services list

Output:

    SERVICE   REGION    URL  LAST DEPLOYED BY          LAST DEPLOYED AT
    ✔  monolith  us-east1 <your url>  <your email>  2019-09-16T21:07:38.267Z

The output shows you several things. You can see your deployment, as well as the user that deployed it (your email address) and the URL that you can use to access the app. Looks like everything was created successfully!

Open the URL provided in the list of services in your web browser and you should see the same website that you locally previewed.

**Note:** You can also view your Cloud Run deployments via Cloud Console. Click Navigation menu ☰ > Cloud Run to see the dashboard containing the service that you deployed.

## Reference :

Google Codelabs  :
[Deploy a website with Cloud Run](https://codelabs.developers.google.com/codelabs/cloud-run-deploy#3)

Google Skill Boost : 
[Docker Essentials: Containers and Artifact Registry](https://www.skills.google/focuses/127756?catalog_rank=%7B%22rank%22%3A3%2C%22num_filters%22%3A0%2C%22has_search%22%3Atrue%7D&parent=catalog&search_id=58142152)