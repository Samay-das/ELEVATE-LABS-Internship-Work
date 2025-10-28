# Day 6 :  Host and Deploy a Web Application on the Cloud.

## 1. Overview.
App Engine allows developers to focus on doing what they do best, writing code, and not what it runs on. Developers upload their apps to App Engine, and Google Cloud takes care of the rest. The notion of servers, virtual machines, and instances have been abstracted away, with App Engine providing all the compute necessary. Developers don't have to worry about operating systems, web servers, logging, monitoring, load-balancing, system administration, or scaling, as App Engine takes care of all that. Developers only need to focus on building solutions for their organizations or their users.

Google App Engine applications are easy to create, easy to maintain, and easy to scale as your traffic and data storage needs change. With App Engine, there are no servers to maintain. You simply upload your application and it's ready to go.

The App Engine standard environment provides application-hosting services supporting the following languages: Python, Java, PHP, Go, Node.js, and Ruby. 

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

### 3. Write the web app
After Cloud Shell launches, you can use the command line to invoke the Cloud SDK gcloud command or other tools available on the virtual machine instance. You can use your $HOME directory in persistent disk storage to store files across projects and between Cloud Shell sessions. Your $HOME directory is private to you and cannot be accessed by other users.

Let's get started by creating a new folder in your $HOME directory for the application:


    mkdir ~/helloworld
    cd ~/helloworld

Create a file named main.py:

    touch main.py

Edit the file with your preferred command line editor (nano, vim, or emacs) or by clicking the Cloud Shell Editor button:

![image_7](https://codelabs.developers.google.com/static/codelabs/cloud-app-engine-python3/img/10af7b1a6240e9f4.gif)

To directly edit the file with Cloud Shell Editor, use this command:

    cloudshell edit main.py

main.py

    import flask

    # If `entrypoint` is not defined in app.yaml, App Engine will look for an app
    # called `app` in `main.py`.
    app = flask.Flask(__name__)


    @app.get("/")
    def hello():
        """Return a friendly HTTP greeting."""
        return "Hello World!\n"


    if __name__ == "__main__":
        # Used when running locally only. When deploying to Google App
        # Engine, a webserver process such as Gunicorn will serve the app. This
        # can be configured by adding an `entrypoint` to app.yaml.
        app.run(host="localhost", port=8080, debug=True)

This web app is a simple web service responding to HTTP GET requests with the message Hello World!.

### 4. Define the dependencies

To specify the dependencies of your web app, go back to the terminal and create a requirements.txt file in the root directory of your project, with the exact version of Flask to use:

    touch requirements.txt

To edit the file with Cloud Shell Editor, use this command:

    cloudshell edit requirements.txt

requirements.txt

    # https://pypi.org/project/Flask
    Flask==3.0.2

The main dependency in your web app is Flask.

### 5. Configure the deployment
To deploy your web app to App Engine, you need an app.yaml file. This configuration file defines your web app's settings for App Engine.

From the terminal, create and edit the app.yaml file in the root directory of your project:

    touch app.yaml

To edit the file with Cloud Shell Editor, use this command:

    cloudshell edit app.yaml

app.yaml

    runtime: python312

With this simple web app, you just need to specify your Python version (e.g. 3.12 here). For more complicated web apps, you can configure additional settings, like scaling, handlers for static files, additional handlers, and other application elements like environment variables and service names. For more information and a list of all supported items, see the app.yaml reference.

### 6. Deploy the web app

A few steps are being skipped here. In a standard development cycle, before deploying, you would first set up a local development environment, install the same dependencies locally, and test the app locally. See Setting up a Python development environment.

From the terminal, check the content of your directory:

    ls

You should have the 3 following files:

    app.yaml  main.py  requirements.txt

Deploy your web app with the following command:

    gcloud app deploy

The first time, you need to choose a deployment region:

    Please choose the region where you want your App Engine application
    located:

     [1] asia-east2
    ...
     [7] australia-southeast1
     [8] europe-west
     [9] europe-west2
    ...
     [12] northamerica-northeast1
     [13] southamerica-east1
    ...
     [19] us-west4
    ...
    Please enter your numeric choice:

Confirm to launch the deployment:

    Creating App Engine application in project [PROJECT_ID] and region [REGION]....done.
    Services to deploy:

    descriptor:      [~/helloworld/app.yaml]
    source:          [~/helloworld]
    target project:  [PROJECT_ID]
    target service:  [default]
    target version:  [YYYYMMDDtHHMMSS]
    target url:      [https://PROJECT_ID.REGION_ID.r.appspot.com]

    Do you want to continue (Y/n)?

Your app gets deployed:

    Beginning deployment of service [default]...
    Created .gcloudignore file. See `gcloud topic gcloudignore` for details.
    Uploading 3 files to Google Cloud Storage
    100%
    File upload done.
    Updating service [default]...done.     
    Setting traffic split for service [default]...done.
    Deployed service [default] to [https://PROJECT_ID.REGION_ID.r.appspot.com]

Your web app is now ready to respond to HTTP requests on 

    https://PROJECT_ID.REGION_ID.r.appspot.com.

### 7. Test the web app

Your web app is ready to respond to HTTP requests on 

    https://PROJECT_ID.REGION_ID.r.appspot.com.

First, retrieve your web app hostname with the gcloud app describe command:

    APPENGINE_HOSTNAME=$(gcloud app describe --format "value(defaultHostname)")

Test your web app with this simple HTTP GET request:

    curl https://$APPENGINE_HOSTNAME

You should get the following answer:

    Hello World!

## Reference :

Google Codelabs  :
[Getting started with App Engine (Python 3)](https://codelabs.developers.google.com/codelabs/cloud-app-engine-python3#6)

Google Skill Boost : 
[App Engine: Qwik Start - Python](https://www.skills.google/focuses/1014?parent=catalog)