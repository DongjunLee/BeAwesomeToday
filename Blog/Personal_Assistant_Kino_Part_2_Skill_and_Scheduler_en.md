# Personal Assistant Kino Part 2 - Skill & Scheduler.

The Kino is a project to know about myself through [Quantified Self](http://quantifiedself.com/), automate things to repeat and improve the quality of life.

![images](../images/quantified_self_logo_2x.gif)

From http://quantifiedself.com/


## Skill & Scheduler

Following, which [briefly introduced Kino](), I will talk about the Skill and Scheduler components. These two components came out with the following thoughts.

How do I create a smart personal assistant bot for me?

1. I would like to customized function using the services I use, such as [IFTTT] (https://ifttt.com).
2. If so, I will name and manage these functions as Skill.
3. Finally, what if I could get this skill to run at the time I wanted?


Here is a little more detail of this Skill and Scheduler.  
Create a custom function by wrapping the API of the external service. Then use the crontab function to run the function at a set time.

The details of the two functions are presented and i design tasks how to implement it. I had one thing that I thought was important during this kino project. (My favorite language is Python.) 'Use **Python 3.6**'s new features.' So the description of the implementation also includes them. I hope that readers of this article feel the charm of Python 3.6.


### Skill

Since the Skill can be added continuously, it was necessary to divide from the **project structure**.

```
- functions.py : Files to register functions in skills
- skills : Directory of Skill
	- weather.py : Weather skill
	- todoist.py : Todoist skill
	- ....
```

Next is 'how to use Skill'.  

I was thinking of using Skill with a simply keyword matching at first, but it was not a good idea to typing all the Keywords. Because typing all keywords requires a great deal of effort, I have thought of a more compact way to use them. So the added **Disintegrator**.

I use simple NLP here. In English, [nltk package](http://www.nltk.org/) supports morphological analysis and part-of-speech tagging. What you need to do with nltk is simple. To make the sentence simple.

```python
class EngDisintegrator:

    def __init__(self):
        self.stopwords = set(stopwords.words('english'))
        self.lemmatizer = WordNetLemmatizer()
    
    def convert2simple(self, sentence=""):
        tokenized = word_tokenize(sentence)
        tokenized = self._filter_punctuation(tokenized)
        tokenized = self._filter_stopwords(tokenized)
        return " ".join(self.__lemmatize(tokenized))
```

Let's see what happens when the actual sentence passes through this EngDisintegrator!

```
>>> disintegrator.convert2simple(sentence="Let me know the weather now.")
'Let know weather'
>>> disintegrator.convert2simple(sentence="How is the weather?")
'How weather'
```

This is a simple sentence, and you can use these sentences to extract the keywords.

Now the question is, where do we store the Keyword? And how do you manage the param needed for your skill?

At first, How about create a new file on the outside and have information about the skill? Hmm.. Rather than, what if read the information about the Skills and create a file called ```skills.json``` when start the Bot.? I thought this was a good idea, and I use python Built-in attributes ```__doc__``` and ```__annotations__```.

Below is the code used in the Skill.

```python
def forecast(self, timely: str="current"):
    """
    keyword: ["날씨", "예보", "weather", "forecast"]
    description: "Weather forecast"
    icon: ":sun_with_face: "
    """

    ...
    weather.forecast(timely=timely)
```

```timely: str``` is a type annotation feature in Python 3.6.
If you enter the type of timely,

```python
>>> forecast.__annotations__
{"timely": str}  // can get skill's params!
```

```__annotations__``` to get the required param for using the skill. Similarly, you can use ```__doc__``` to retrieve the following keywords, descriptions, and icons. So, when you collect this information and create a skills.json file, then the registration of the Skill be end.

Next, write keyword matching code using ```any()```, ```all()``` provided by Python, and use ```Regex(regular expression)``` Write the code to pass Param!   
You can easily use the weather skills as shown below. It's easy, is not it?

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/en/kino-skill-example-en1.png?raw=true)


### Scheduler

Now that i've added the skill and created a structure that can use, Let's try to create a scheduler. Python has a Package that makes it easy to use scheduling called [schedule](https://github.com/dbader/schedule).

Below is a simple example code on the schedule Github page. It is very intuitive without any other explanation.

```python
schedule.every(10).minutes.do(job)
schedule.every().hour.do(job)
schedule.every().day.at("10:30").do(job)
schedule.every().monday.do(job)
schedule.every().wednesday.at("13:15").do(job)
```


What I need to do is with schedule is..

1. Run in the background.
	- Using **Threading**
2. Dynamically scheduling.
	- Using **getattr** that Python's built-in funciton

If you want to find out more about this section's code, please refer to [Github repository](https://github.com/DongjunLee/kino-bot)! Next, using this function only has some limitations, so we add the following condition.

**Between** : Indicates the time interval. In schedule, we added it every 10 minutes, every hour, so we made it possible every 10 minutes and every hour at specific time interval.

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/en/kino-read-between.png?raw=true)

Now I am ready to use Scheduling as I want! The next step is to simply register Scheduling and run it in background with natural language.

Again, we use keyword matching.

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/en/kino-create-by-ner.png?raw=true)

Now we can add Scheduling function!


## Example

Now let's see how these functions are actually used!

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/en/kino-create-job.png?raw=true)

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/en/kino-start-job.png?raw=true)

When kino start to working, it will proceed according to the registered Scheduling Jobs.

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/en/kino-read-schedule.png?raw=true)

- weather forecast every 2 hours from 10:00 to 22:00
- 8 am daily briefing
- 10 pm notify remain tasks

The above examples are actually the scheduling jobs I use.  
Kino now has [**26 Skills**](https://github.com/DongjunLee/kino-bot#current-skills).

Do not you think that you can act like a personal assistant? :D  
So Kino gets smarter and becomes a assistant for me!

Next, I'll take a look at the **T3** feature, which allows you to easily manage the tasks in Todoist through the card in Trello, automatically record time in Toggl, and finally receive task reports.

All code can be found [here](https://github.com/DongjunLee/kino-bot).
Anyone who helps make Kino smarter is always welcome :)