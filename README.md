# Hosting your Python app

**To start this please fork this repository on GitHub and clone your forked version!**

0. If you are a Mac user please see [Homebrew installation section](#install-homebrew)
1. Install command line tools for Heroku:

**Mac OS**
```
brew install heroku/brew/heroku
```

**Windows**

Please visit [setup page](https://devcenter.heroku.com/articles/getting-started-with-python#set-up). Download and run correct installer for your version of Windows.

2. Restart terminal for changes to take effect.
3. Run command shown below and follow instructions to login:
```bash
heroku login
```
4. In terminal navigate to the root folder of the app you would like to deploy (**please note:** this app **must** be commited to GitHub).
5. Create new app in Heroku by running:
```bash
heroku create
```
6. Navigate to the URL shown in the terminal. URL in question should look similar to:
```
https://pure-refuge-43369.herokuapp.com/
```
Currently it will be showing default page.

7. Install `gunicorn` with `pip`:
```bash
python -m pip install gunicorn
```
`gunicorn` is a simple server which works a lot better when hosted and can be used together with Flask.

8. Remove `app.run()` from the main file of your app.
9. Check that everything is working locally by running:
```bash
gunicorn <MAIN FILE NAME>:<APP VARIABLE NAME>

# For example in the case of this repo
gunicorn server:app
```
10. Create three files in the root of your app:
  - `runtime.txt` - containing python version;
  - `requirements.txt` - containing all things `pip` needs to install (including `gunicorn`);
  - `Procfile` - instruction to Heroku how to run your app (this is the same to how you run it locally):
  ```
  web: gunicorn server:app
  ```
11. Commit all the changes and push them to GitHub.
12. Now we are finally ready to push our app into Heroku. When we created app a while back Heroku also created a brand new GitHub repository linked to ours. To update it with our code we need to run the following command:
```bash
git push heroku master
```
13. Your app should now be running on the URL we opened in the browser before!
14. If you tried sending a greeting you probably noticed that something is wrong. Instead of a confirmation we recieve an error. This is because out API key and domain are not getting passed to the app running in Heroku. Lets fix this!
15. First we will make sure that our app has access to what it needs. We do not want to commit out `.env` file to GitHub for security reasons (it contains secret information!), so we will define necessary variables in Heroku itself. For this please navigate to [main portal](https://dashboard.heroku.com/apps), select your app, go to `Settings` and find section `Config Vars`. In this section select `Reveal Config Vars` and then copy-paste name and value from your `.env` file. You are done with this part!
16. So far our app only know how to work with `.env` file. In Heroku on the other hand we defined something called enviroment variables, this is variables which exist on the machine running our app itself rather than in a file. Lets get `send_message` to understand this. We will need to replace code in `send_message.py` with the following:
```py
import requests
from dotenv import load_dotenv
import os
load_dotenv() # This will load .env file as enviroment variables allowing you to continue developing locally as before

def send_message(email, message):
  mail_api_key= os.getenv("MAILGUN_API_KEY") # This will read necessary variables from enviroment variables
  mail_domain = os.getenv("MAILGUN_API_DOMAIN")

  return requests.post(
    "https://api.mailgun.net/v3/{}/messages".format(mail_domain),
      auth=("api", mail_api_key),
      data={"from": "Excited User <mailgun@{}>".format(mail_domain),
        "to": [email],
        "subject": "Greetings!",
        "text": message})
```
17. Commit your changes, push to your repo and push to Heroku the same way we done it before.

Congratulations! You just deployed your first app for the whole world to see!

## Install Homebrew
Homebrew gives us an easy way to install any extra tools we need, primarily for our terminals. It keeps them all in one place, so we don't need to do endless internet searches, and makes updating what we need a lot simpler.

1. Check do you have Homebrew installed by running
```bash
brew -v
```
if Homebrew is installed you will see something similar to the output shown below:
```
Homebrew 2.1.4
Homebrew/homebrew-core (git revision 4b37f; last commit 2019-06-09)
```
2. **Only follow this step if Homebrew is not installed**. To install it run:
```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

If you want to learn more about this tool, please visit [Homebrew website](https://brew.sh/)!

