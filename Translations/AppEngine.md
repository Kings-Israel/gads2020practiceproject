# GOOGLE CLOUD FUNDAMENTALS: GETTING STARTED WITH APP ENGINE

## OBJECTIVES:

 - Initialize the app engine.
 - Preview an App Engine application running locally on cloud shell.
 - Deploy an App Engine application, so that others can reach it.
 - Disable an App Engine application, when you no longer want it to be visible

## STEPS:

1. List the active account

    `gcloud auth list`

2. List the project ID

    `gcloud config list project`

3. Initialze the app with the project ID and choose the region

    `gcloud app create --project=$DEVSHELL_PROJECT_ID`

4. Clone the sample application in helloWorld directory

    `git clone https://github.com/GoogleCloudPlatform/python-docs-samples`

5. Change to source directory

    `cd python-docs-samples/appengine/standard_python3/hello_world`

6. Update packages list in cloud shell

    `sudo apt-get update`

7. Set up virtual environment where the app will run

    `sudo apt-get install virtualenv`

    `virtualenv -p python3 venv`

8. Activate the environment

    `source venv/bin/activate`

9. Navigate to project and install dependencies

    `pip install -r requirements.txt`

10. Run the application

    `python main.py`

11. DEPLOY APP IN APP ENGINE

    1. Navigate to source directory

        `cd ~/python-docs-samples/appengine/standard_python3/hello_world`

    2. Deploy the app

        `gcloud app deploy`

12. Get link to the app

    `gcloud app browse`

# Open app in browser using link

# To disable the app open the console using the command

`gcloud app open-console`

# Select the app and click disable

# Confirm by entering the App ID

# The app can no longer be reached using the link

