# Day 4 :  Deploy a Serverless Function to the Cloud

## Cloud Run Function 

A Cloud Run function is a piece of code that runs in response to an event, such as an HTTP request, a message from a messaging service, or a file upload. Cloud events are things that happen in your cloud environment. These might be things like changes to data in a database, files added to a storage system, or a new virtual machine instance being created.

Since Cloud Run functions are event-driven, they only run when something happens. This makes them a good choice for tasks that need to be done quickly or that don't need to be running all the time.

For example, you can use a Cloud Run function to:

* automatically generate thumbnails for images that are uploaded to Cloud Storage.
* send a notification to a user's phone when a new message is received in Pub/Sub.
* process data from a Cloud Firestore database and generate a report.

You can write your code in any language that supports Node.js, and you can deploy your code to the cloud with a few clicks. Once your Cloud Run function is deployed, it will automatically start running in response to events.

## Function Framework Overview 

When writing functions source code, you must use the Functions Framework, an open source library for writing Cloud Run functions. With the Functions Framework, you can write lightweight functions that run in Cloud Run and in other environments, including your local development machine and Knative-based environments.

The Functions Framework lets you:

* Invoke a Cloud Run function in response to a request.
* Automatically unmarshal events conforming to the CloudEvents specification, an industry-standard specification for describing event data in a common way.
* Start a local development server for testing.

The Functions Framework provides an interface for building modular services. To use the Functions Framework in your source code, specify the following:

* Function entry point
* Signature type

### Function entry point
Your source code must define a function entry point, which is the code that runs when Cloud Run invokes your function. You specify this entry point when you deploy your function.

How you define the entry point depends on the language runtime you use. Some languages use a function as their entry point, while others use a class.

### Signature Type
When you write the source code of a function with the Functions Framework, you must specify one of the two signature types:

1. HTTP functions: Registers an HTTP handler function. Use an HTTP function when your function needs a URL endpoint and must respond to HTTP requests, such as for webhooks.
2. Event-driven functions, also known as CloudEvents functions: Registers a CloudEvents handler function. Use an event-driven function when your function is triggered directly in response to events within your Google Cloud project, such as messages on a Pub/Sub topic or changes in a Cloud Storage bucket.

----
### Source directory structure
The Functions Framework is supported in a number of programming languages. The language runtime you choose and the type of function you want to write determines how to structure your code and implement your function.

For Cloud Run to locate your function definition, each language runtime has requirements for structuring your source code.


The basic directory structure for Python functions is as follows:

    .
    ├── main.py
    └── requirements.txt

Cloud Run loads source code from a file named main.py at the root of your function directory. You must name your main file main.py.

Your requirements.txt file must include the Functions Framework for Python as a dependency:

    functions-framework==3.*

The code in your main.py file must define your function entry point and can import other code and external dependencies as normal. The main.py file can also define multiple function entry points that can be deployed separately.

See the Python runtime overview and the Functions Framework for Python for more details.

---

### Write the sample function
1. Create a new directory named helloworld and change directory into it:

        mkdir helloworld
        cd helloworld

2. Create a requirements.txt file in the helloworld directory, to specify Python dependencies:

        functions-framework==3.9.2
        flask==3.0.3
        google-cloud-error-reporting==1.11.1
        MarkupSafe==2.1.3
    This adds packages needed by the sample.

3. Create a main.py file in the helloworld directory with the following Python sample:

        import functions_framework
        @functions_framework.http
        def hello_get(request):
            """HTTP Cloud Function.
            Args:
                request (flask.Request): The request object.
                <https://flask.palletsprojects.com/en/1.1.x/api/#incoming-request-data>
            Returns:
                The response text, or any set of values that can be turned into a
                Response object using `make_response`
                <https://flask.palletsprojects.com/en/1.1.x/api/#flask.make_response>.
            Note:
                For more information on how Flask integrates with Cloud
                Functions, see the `Writing HTTP functions` page.
                <https://cloud.google.com/functions/docs/writing/http#http_frameworks>
            """
            return "Hello World!"

### Deploy the function

To deploy your Cloud Run function, follow these steps:

1. Deploy the function by running the following command in the directory that contains the sample code:

        gcloud run deploy python-http-function \
        --source . \
        --function hello_get \
        --base-image python313 \
        --region REGION \
        --allow-unauthenticated

Replace REGION with the Google Cloud region of the service where you want to deploy your function. For example, europe-west1. 

2. When the deployment is complete, the Google Cloud CLI displays a URL where the service is running. Open the URL in your browser to see the output of your function.
<br>

>Success: You deployed an HTTP Cloud Run function using the gcloud CLI.

### Clean up
To avoid additional charges to your Google Cloud account, delete all the resources you deployed with this quickstart.

#### Delete your repository

Cloud Run doesn't charge you when your deployed service isn't in use. However, you might still be charged for storing the container image in Artifact Registry. To delete Artifact Registry repositories, follow the steps in Delete repositories in the Artifact Registry documentation.

## Reference :

Google Cloud  :
[Write Cloud Run functions](https://cloud.google.com/run/docs/write-functions#python)

Google Cloud  :
[Quickstart: Deploy a Cloud Run function using the gcloud CLI](https://docs.cloud.google.com/run/docs/quickstarts/functions/deploy-functions-gcloud#python_1)

Google Skill : [Cloud Run Functions: Qwik Start - Command Line](https://www.skills.google/focuses/916?parent=catalog)