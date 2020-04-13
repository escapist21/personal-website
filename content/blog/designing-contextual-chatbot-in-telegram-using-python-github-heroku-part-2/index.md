---
title: "Designing Telegram chatbot"
date: 2020-04-12T21:06:00+01:00
draft: true
hideLastModified: false
summary: "Part 2 of designing Telegram chatbots with Python, Github & Heroku"
summaryImage: "images/bot_screenshot.jpeg"
tags: ["bot", "python"]
---
In the last part, we figured out how to set up the coding environment and wrote the Python script that actually runs the bot logic. In this part, we will create a new app in Heroku, set up a webhook in the Python script and push the local git repository to remote git that we had set up. Finally, we will set up our Heroku app to directly access the Github repository and deploy the app.


### Creating a new app in Heroku

Heroku is a cloud application platform that operates as a PaaS (Platform as a Service). It enables developers to build, run and operate applications entirely in the cloud. If you do not have a free Heroku developer account, you can create one by following this link.
Log in to your Heroku account and your app dashboard will be presented to you. The app dashboard showcases all your apps and lets you manage them. To create a new app click on ‘New’ on the top right corner and select ‘Create new app’ from the dropdown

{{< figure src="images/heroku_dashboard.png" width="1280" >}}

In the next step, enter a name for the app and click ‘Create app’

{{< figure src="images/heroku_create_new_app.png" width="1280" >}}

Once the app is successfully created, you will be taken to the app management screen

{{< figure src="images/heroku_app_management.png" width="1280" >}}

This page allows you to deploy your app, view logs etc. among other operations. We will return to this page later on.


### Setting up  webhook

A webhook is an HTTP push API, which is used to deliver data to other applications in real-time. This is an improvement over typical HTTP push APIs since there is no need for frequent pushing to emulate the real-time feel. The Telegram bot API provides inbuilt methods to set up a webhook.
To set up the webhook open the python script where you have been writing the bot logic and append the following code in the main function only. The rest of the file remains as it is.

{{< gist escapist21 13ec6fc199f4be6af13270610b01ffd8 >}}

##### Explaining the appended code
**Line 6**: Set the name of the app that you created in Heroku in the last section to the APPNAME variable

**Lines 27–30**: Start a webhook by calling the `updater.start_webhook()` method and set the arguments to the values as shown in the example

**Line 31**: Set the webhook by calling the `updater.set.Webhook()` method and pass the URL of the Heroku app as shown in the example

**Line 36**: Comment out the `updater.start_polling() method`. This is no longer required since webhook has been set

**Line 39**: Comment out the `updater.idle()` method as well

### Pushing the code to Github repository
We are now ready to push our code to the Github repo. But first three additional files need to be created.

* _requirements.txt_: This file lists all the python packages that will be installed by Heroku in its environment. We have been already working within a virtual environment, and to create this file just type the following command from the terminal
`pip freeze > requirements.txt`

* _Procfile_: All Heroku apps include a Procfile. The Procfile specifies the commands that are to be executed by the app on start up. It typically follows this format:
`<process type>: <command>`
Create the Procfile by running `touch Procfile`
Open this Procfile in your editor and add `web: python telegram_bot.py`

* _.gitignore_: This file maintains a list of all the files that do not need to be pushed to the Github repo. Typically this will contain all local environment files. Since we have been using a virtualenv named venv our file should list
`venv/`
`.venv`
`venv.bak`

Once these three files have been created, issued these commands from your terminal

{{< codeWide language="shell" >}}
git add.
git commit -m "first commit"
git push -u origin master
{{< /codeWide >}}

All the files in your local git repo will be now available in your remote Github repository.

### Deploying the app on Heroku
Now return to your Heroku app page. Under the ‘**Deploy**’ tab, three deployment methods will be listed. We will select the **Github method**.

Give the necessary permissions to Heroku to access your Github profile. Once the authorization has been set up, search for the name of your repository from the search bar and click on ‘**Connect**’.

Once the repository has been connected to the Heroku app, click on ‘**Deploy branch**’ under the ‘**Manual deploy**’ section, pointing to the ‘**main**’ branch. The app will now build itself and if all the steps are followed correctly, your bot will be hosted.

**Please note** that visiting your app URL will return a ‘**404: page not found**’ error but that is alright since the script does not necessarily return anything. If problems are encountered while starting the app, visiting the URL will display the error message.

So, that’s it for this tutorial. Happy coding!
