# Not enough friends? Get a bot!
 
# Introduction 
 
When you start reading about Machine Learning and [Recurrent Neural networks](https://en.wikipedia.org/wiki/Recurrent_neural_network) there is a high chance that the first thing you want to do is writing a bot. I was in that group.
 
So, I coded a Machine Learning model. But even after training it for a few days I wasn't able to get results as cool as the bots I used to see on Twitter. So after digging into how the community was implementing these bots, I found that most of the guys were using [Markovify](https://github.com/jsvine/markovify).
 
After a few hours reading and coding I was able to get my own bot, called [TelegramBotFriend](https://github.com/kblok/TelegramBotFriend), ready to run in a Docker container.
 
# Recipe
 
So what do we need to create our own bot? We need these ingredients:
 
 * A data source.
 * A text provider.
 * A content generator.
 * A platform agnostic bot.
 * A platform connector.
 * An orchestrator.
 
# Data Source
 
I think this is the most important ingredient. If you don't have a good rich bunch of text, your bot will sound quite dumb. When I say rich I mean over 10.000 lines of text, the more the better.
 
Where can you get that text from?
 
 * In my case, I went to one of my friend's WhatsApp group and exported the group history.
 * Your Twitter account, and even your friend's Twitter accounts.
 * Your blog.
 * Slack.
 
You need to collect as much text as possible.
 
You might need to post-process that text because, as we all know, “garbage in, garbage out”. You might want to remove URLs or garbage generated by the source, e.g. WhatsApp adds "Audio ignored" "Image ignored" if you export the history without the content, but you should totally leave emojis :).
 
 
# Text Provider
 
The text provider is a class with the following signature
 
```
def TextProvider(object):
    def get_text(self):
        #Loads the data source and returns its content
    def add_text(self):
        #Add new content to the file
```
 
Why do we need a class for this simple task? 
In my case I wanted to get that file from dropbox so the docker instance could be easily removed or recreated. You might want to use your local file system or Google Drive as long as you create a class that matches that signature.
 
I added an `add_text` function because we might want to feed our history file with new content we get from our platform.
 
# Content Generator
 
Now we get to the fun part. The content generator will be responsible for learning from the source data and return new content when called.
 
A content generator looks like this:
 
```
dev ContentGenerator(object):
    def load(self):
        #Setups the instance using a TextProvider
    def get_message(self, text):
        #It expects a text and try to returns a message based on that input
``` 
 
As I mentioned in the introduction I implemented a content generator using [Markovify](https://github.com/jsvine/markovify), but it could be any other content generator, such as a trained RNN.
 
Most of these content generators would (optionally) expect a seed text and return a string based on that text. You can process the content you get as an argument, removing stop words, and trying to get some meaningful reply to that content.
 
I used [NLTK](http://www.nltk.org/) to remove stop words and then I just iterate over the remaining text trying to get a valid content from Markovify, if I don't, I just ask for a text without a seed.
 
# Platform Agnostic Bot
 
Though my bot is called **Telegram** BotFriend, after a few hours coding I found out that it could work with any platform. I would need to write all the logic in a platform agnostic class and then I would need to code a platform specific class that uses my abstract bot, which looks like this:
 
```
def AbstractBot(object):
    def process_incoming_message(self, chat_id, text):
        #Expects a text and returns a reply using a Content Generator
        #chat_id can be set to 0 if it's not a concept valid in the platform 
```
 
The abstract bot will be called by the platform specific bot and:
 
 * Return a string if he was mentioned (yes, it's a he).
 * It will join in and return a message after a random number of messages received, here's where the chat_id comes into place.
 * It will, optionally, add the new message to the source data.
 
# Platform Specific Bot
 
A platform specific bot, such as a TelegramBot, a TwitterBot, etc, would receive a platform agnostic bot in the `__init__` function and then it would call the `process_incoming_message` method when needed.
 
My Telegram bot just follows this simple signature:
 
```
class TelegramClient(object):
    def __init__(self, abstract_bot, token):
```
 
# Orchestrator
 
As you can see, the data source could be any data, the text provider any provider, a content generator any generator, and so on. Now we need something responsible for grabbing all the ingredients setting those up and connect each other.
 
In my implementation a `bot_friend.py` script gets the arguments from the environment (super easy to setup in docker) or from the command arguments (easy to debug). And then cooks the bot:
 
``` 
text_provider = DropboxTextProvider(dropbox_access_token, dropbox_file)
provider = MarkovifyProvider(language, text_provider)
meme_text_provider = DropboxTextProvider(dropbox_access_token, meme_file)
meme_provider = MemeProvider(meme_text_provider)
abstract_bot = AbstractBot(name, provider, auto_feed, meme_provider)
TelegramClient(abstract_bot, token)
```
 
Yes, of course, I forgot to mention, I also have a MemeProvider, a bot is not a bot if it's not able to use memes.
 
# Use it out of the box
 
You can use my bot as is just following these steps:
 
### Create your own TelegramBot 
 
There are many tutorials in the web but it's super easy:
 
* Open a chat with BotFather
* Type: `/newbot`
* It will ask you for a name and it will give you the token
* Then you have to type `/setprivacy` and set it to `disable` in order to be able to read the incoming messages
 
### Create a Dropbox Access token
 
* Go to https://www.dropbox.com/developers/apps/create
* Create a new "Dropbox API"
* It will give you a token
* Copy in the folder created in Dropbox the chat and the meme list file.
 
### Docker
 
You can setup a new docker container using the instructions in [the repo](https://github.com/kblok/TelegramBotFriend).
 
 
Note: The meme list file is comma separated file, something like this:
```
trigger word(s),meme url
```
 
# Final words
 
What I liked about this little project is that, to begin with, as I'm not a Python developer, it helped me to get into the language. If you're interested in the project you're more than welcome to join on Github! I'd appreciate any comment not only with the work itself, but also with code styles and good practices.
 
Second, coding bots is fun, I mean, super fun.
 
And last but not least, this project also opens the door to keep learning new stuff having many things already solved. I can go back to RNN in TensorFlow and it would be only a matter of replacing the MarkovifyContentProvider or I can create a Slack bot replacing the TelegramBot with the Slack one, I could also have both running using the same AbstractBot instance.


