---
title: "Designing Telegram chatbot"
date: 2020-04-09T23:53:00+01:00
draft: true
hideLastModified: false
summary: "Part 1 of designing Telegram chatbots with Python, Github & Heroku"
summaryImage: "images/chatbot.jpg"
tags: ["bot", "python"]
---

Telegram bots are third-party applications that run inside the application. Users can interact with bots by sending them messages, commands and inline requests. Contextual chatbots are a category bots that communicate only through inline requests. They are contextual since they maintain a context and the users can only navigate the pre-defined context. These are especially useful in providing customer service. In this post, we will look at how to design such bots in Telegram using Python.

This will consist of two parts. In the first part, we will register our bot with Telegram, set up our local coding environment and write the code for the bot. In the second part, we will discuss how to host the bot on Heroku which is cloud-based service useful for hosting small web-based applications.

### Registering a new bot with Telegram

To register a new bot go to the Telegram app on your phone and follow these steps:  

1. In the search bar search for the “BotFather”. “BotFather” is a bot that will assist you in creating and managing all your bots  
2. Start BotFather and type /help. This brings up all the possible commands that this bot can handle  
3. Type /newbot or click on this command from this list  
4. Follow the instructions for setting up a new bot which is basically choosing a name and username for your bot. \[Tip: Pick a username which is relevant to what your bot can do\]  
5. Once the bot is successfully created the BotFather will return a token to access HTTP API. Save this token securely with you. We will be using this token to authorize our bot from within the python script

### Setting up a new Python virtual environment

It is good practice to create virtual environments for python projects. A virtual environment helps to keep dependencies required by different projects separate. A virtual environment also helps to easily create a _requirements.txt_ file, which is used by Heroku to create its python environment in the cloud.

While there are few choices for virtual environment tools, we will be using virtualenv for our project. Follow these steps to create a Python virtual environment using virtualenv

* open your terminal in macOS or Linux and create a new project directory

```bash
mkdir telegram-bot && cd telegram-bot
```

* check that virtualenv is installed in your system. If virtualenv is installed this command will return the installation path else it won’t return anything. If it is not installed you can install it using the methods mentioned in this [link](https://virtualenv.pypa.io/en/latest/installation.html)

```bash
virtualenv --v
```

* create a new virtual environment and activate it

{{< codeWide language="shell" >}}
virtualenv venv
source venv/bin/activate
{{< /codeWide >}}

While you can use any name for your environment, venv is a convention that is commonly used. Once the environment is active proceed to install the required python packages for the project

We will be using the [_python-telegram-bot_](https://github.com/python-telegram-bot/python-telegram-bot) library for this project. This library provides a pure Python interface for the [Telegram Bot API](https://core.telegram.org/bots/api). To install this library use the following command

```bash
python -m pip install -U python-telegram-bot
```

### Initialising a Git repository in the project folder

A git repository needs to be initialised in the project folder as Heroku uses a local git repository to deploy the code in their cloud service. While typically the Heroku remote is added to the local git repository to push the commit, for this project we will add a remote from Github, and further connect the Github repository to the Heroku app.

To initialise a local git repository, in your project folder

```bash
git init
```

Also, create a .gitignore file. The .gitignore file lists the name of those files that should be ignored from all commits. We can update this file later

```bash
touch .gitignore
```
Now we will be creating a new repository in Github. Visit your Github account and create a new repository. You can either make the repository public or private. We will assume that the name of Github repository you have created is the same as your project folder. Once the repository is created copy its URL.

Now we need to connect our local repository with our newly created Github repository.

```bash
git remote add origin URL-to-your-Github-repository
```

Once the connection to the remote repository has been established

```bash
git pull origin master
```

The local repository is now ready to push commits to the Github repository

### Writing the Python app

Hurray! Now we can finally start writing the Python script for the bot. This bot will have a very simple design. There is this very famous scikit-learn [machine learning map](https://scikit-learn.org/stable/tutorial/machine_learning_map/index.html) available which can help data scientists decide on which estimator to try depending on the data. While the original map is extensive for this tutorial we only adapt the classification segment (other segments are clustering, regression and dimensionality reduction).

{{< figurehugo src="images/classification.png" caption="Flowchart for Classification" >}}

The script given its size is impossible to host here completely. Please click on this [link](https://gist.github.com/escapist21/52346423fdeb0b1c49d6ccbb66a9cfe2) to access the Github gist for this code. Create a new file in your project folder named _telegram_bot.py._ Open this file in your favourite code editor and paste the code from the file in the Github link.

```bash
touch telegram_bot.py
```
### Explaining the code

Here we will look at parts of the code and try to understand what is happening

{{< codeWide language="python" >}}
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters, CallbackQueryHandler, ConversationHandler
from telegram import InlineKeyboardButton, InlineKeyboardMarkup  
import os
{{< /codeWide >}}

First, we import the necessary classes from the python-telegram-bot library. The first line imports the various handlers that will be required for this program. You are requested to read up about what these handlers do. The second line imports the modules associated with creating the inline keyboard. Since this is a contextual chatbot, only the inline keyboards will be used to communicate with the bot.

{{< figurehugo src="images/bot_screenshot.jpeg" caption="the bot with inline keyboard markup">}}

{{< codeWide language="python" >}}
# States  
FIRST, SECOND = range(2)

# Callback data  
ONE, TWO, THREE, FOUR, FIVE = range(5)
{{< /codeWide >}}

“States” is a list of all conversation steps that will be later used in setting up the _ConversationHandler_. “Callback data” is a list of callbacks that are returned from the inline keyboard upon pressing on it. The “States” along with the “Callback data” will be returned from various functions that will form our conversation logic. Here we just initialize these variables for later use.

{{< codeWide language="python" >}}
def start(update, context):  
    fname = update.message.from_user.first_name

    # Build inline keyboard  
    keyboard1 = [InlineKeyboardButton("<100K samples", callback_data=str(ONE))]  
    keyboard2 = [InlineKeyboardButton(">100K samples", callback_data=str(TWO))]
    
    # create reply keyboard markup  
    reply_markup = InlineKeyboardMarkup([keyboard1, keyboard2])

    # send message with text and appended inline keyboard  
    update.message.reply_text(
        "Hello {}.Let's figure out the best classifer for your data\n\n"
        "How many samples do you have?".format(fname), reply_markup)
    )  
    # tell ConversationHandler that we are in state 'FIRST' now  
    return FIRST  
.  
.  
.

def end(update,context):  
    """Returns 'ConversationHandler.END', which tells the CoversationHandler that the conversation is over"""  
    query = update.callback_query  
    query.answer()  
    query.edit_message_text(  
        "Goodbye, and all the best\n\nIf you need my help again click on /start"  
    )  
    return ConversationHandler.END
{{< /codeWide >}}

the start() function is the first function that is called from within the _ConversationHandler_ that we will define later. It is activated in response to the /start command. Once the _ConversationHandler_ is activated it uses the “State” and “Callback data” variable returned from each function to control the conversation flow.

Following the start() function are the other functions that are called as part of the conversation each having a unique inline keyboard, appended message text and returning a “State” and “Callback data”. Refer to the script to study these functions.

These functions are followed by the end() function. The end function is called when the logic flow within the _ConversationHandler_ reaches the end of the conversation. It returns the _ConversationHandler.END_ signal, which effectively terminates the conversation.

{{< codeWide language="python" >}}
def main():  
    #setting to appropriate values  
    TOKEN = "YOUR ACCESS TOKEN"  
    # set up updater  
    updater = Updater(token=TOKEN, use_context=True)  
    # set up dispatcher  
    dispatcher = updater.dispatcher  
    #print a message to terminal to log successful start  
    print("Bot started")

    # Set up ConversationHandler with states FIRST and SECOND  
    conv_handler = ConversationHandler(  
        entry_points=[CommandHandler(command='start', callback=start)],  
        states={  
            FIRST: [CallbackQueryHandler(linear_svc, pattern='^' + str(ONE) + '$'),  
                   .  
                   .  
                   .  
                   ],  
            SECOND: [CallbackQueryHandler(end, pattern='^' + str(ONE) + '$'),  
                   .  
                   .  
                   .  
                   ]  
                },  
            fallbacks=[CommandHandler(command='start', callback=start)]  
        )

    # add ConversationHandler to dispatcher  
    dispatcher.add_handler(conv_handler)  
      
    # start the bot  
    updater.start_polling()

    # run the bot until Ctrl+C is pressed  
    updater.idle()

{{< /codeWide >}}

The _Updater_ class continuously fetches new updates from Telegram and passes it to the _Dispatcher_ class. Once the _Updater_ object is created, it is used to create a _Dispatcher_ object and they are then linked together in a queue. Different types of Handlers can be registered with the Dispatcher, which then sorts all the updates received from Telegram according to the registered Handlers. For example, here we add a _ConversationHandler_ to the dispatcher.

The _ConversationHandler_ manages four collections of other handlers. In this example, three such collections are used namely _entry_points_, _states_ and _fallbacks_.

The _entry_point_ collection is a list used to initiate the conversation. In this example, a _CommandHandler_ class is used which responds to the ‘start’ command.

The _states_ collection is a dictionary containing the different conversation steps and one or more associated handlers. In this example, all the conversation steps are associated with _CallbackQueryHandler_ since all our updates from the app are in the form of callbacks associated with pressing an inline keyboard button.

{{< figurehugo src="images/flowchart.jpeg" >}}

The flowchart is used to design the conversation steps as defined in the _states_ collection.

The _fallbacks_ collection is a list that is used if the user currently in conversation returns an update which is not of the expected type. For example, a text update is sent when the expected update was a command. This prevents the bot from breaking.

Click on this [link](https://vimeo.com/405917388) to watch a video of the bot in action

Run the python file from the terminal

```bash
python telegram_bot.py
```

and your bot will spring to life ready to converse with you.

That will be it for this long post. In the next part, we will make small changes to the code so that it is ready to be hosted on Heroku, and finally discuss the steps to host it.
