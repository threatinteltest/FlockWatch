# DOCUMENTATION IN PROGRESS

# FlockWatch

FlockWatch is a tool meant to help researchers build better data collections from social media platforms and other websites. It looks at existing datasets built around a list of collection terms, then recommends terms that the researcher might want to add to their collection criteria. FlockWatch creates reports of the words it recommends, and it also sends you an email every time it finishes running to let you know how many words it recommended.  
 FlockWatch can be run on a Linux-based server or a Mac computer (desktop or laptop), but it has not been tested on a Windows machine. It can be set to run every so often indefinitely, or it can be run as a one-off process.

## User skills required for FlockWatch

In order to use FlockWatch, you should:
* be at least minimally comfortable with python3
* know how to edit files using vim, nano, or another command-line text editor (though you can avoid this if you are using FlockWatch on a personal computer rather than a server) 

## Getting data to FlockWatch
FlockWatch was designed to work with Twitter data collected by [STACK](https://github.com/bitslabsyr/stack). If you use STACK, FlockWatch knows how to find collection terms and data about tweets automatically based on the STACK project name.  

If you don't use STACK, you can tell FlockWatch to look for text data in a CSV. With a CSV, you can use data collected from anywhere -- Facebook, Reddit, forums, even offline or digitized sources. You will need to manually provide the collection terms used to collect that data in [config.py](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L21). Note that if you use a CSV, FlockWatch should only be used as a one-off process.  

### Installation and setup
1) Clone the code to your server or computer using `git clone https://github.com/sjacks26/FlockWatch.git`. You should run this command from a directory that your user has write permissions in; otherwise, you can run ServerReport as sudo.  
2) Rename `config_template.py` to `config.py`.
3) Change the parameters in [config.py](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py) to suit your needs. See below for an explanation of these parameters.  

### Setting [config.py](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py) parameters

Before you run FlockWatch, you need to set a number of parameters in the config file:
1) Tell FlockWatch whether it should [run repeatedly](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L4) in the background.  
    * If it should, tell FlockWatch [how often to run](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L5).  
2) Tell FlockWatch whether to get data [from Mongo](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L9) (if you are using STACK to collect data) or [from a CSV](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L10).
    * If Mongo:  
        a. Tell FlockWatch whether Mongo is [password-protected](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#12). If it is, give FlockWatch the Mongo [username](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L13) and [password](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L14).     
        b. Tell FlockWatch whether you want it to [ignore retweets](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L15) or include them when looking for trending or co-occurring terms.
    * If CSV:  
        a. Tell FlockWatch [where](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L18) to find the CSV.  
        b. Tell FlockWatch the [name](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L19) of the column containing the information about when a message was created.  
        c. Give FlockWatch the [list of terms](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L21) used to collect the data in the CSV.
3) Tell FlockWatch what [time interval](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L15) to use when calculating trending rates. The recent block of text will include data from the interval closest to the start time (t2, by default, the current time), and the previous block of text will include data from the interval before the recent block of text (t1). You can also tell FlockWatch that you want it to use more than one interval. If you provide more than one interval, it will generate output for each interval separately.  
4) Tell FlockWatch whether you want it to [start with messages sent right now or from some previous point in time](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L25).  
5) Tell FlockWatch the [fewest number of letters](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L26) you want in any terms it suggests to you.  
6) Tell FlockWatch whether you want it to [find trending terms](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L27).  
    * Give FlockWatch the [minimum trending rate](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L25) that you're interested in. The trending rate is calculated with this formula: `(t2 - t1)/((t2 + t1)/2) * 100`. The maximum trending rate (if a term appears in the more recent set of messages but not in the older messages) is 200.  
7) Tell FlockWatch whether you want it to find [terms that repeatedly appear in the same message as each collection term](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L28).  
    * Give FlockWatch the [lower threshold for co-occurrence rates](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L30) that you want it to report. Each time a given word appears in the same message as a given collection term, the co-occurrence rate for that word-collection term pair increases by 1 / (the number of messages that contain that collection term). This means that if every message that contains a particular collection term also contains a particular term, the co-occurrence rate for that word-collection term pair will be 1; if half of the messages that contain a particular collection term also contain a particular term, the co-occurrence rate for that word-collection term pair will be .5.  
9) Tell FlockWatch if you want to [stop having a term suggested after a certain number of times](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L32). (For example, you might want FlockReport to stop suggesting that you use "bomb" as a collection term after it recommends that term a couple of times, since you realize that "bomb" can mean lots of different things and thus isn't a very good collection term on its own.)  
     * If you want FlockWatch to only report terms a certain number of times, tell FlockWatch what that [limit](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L33) is.  
12) If you have a list of collection- or platform- specific [stopwords](http://www.nltk.org/book/ch02.html#stopwords_index_term) that you want it to use, tell it where to find the [file containing that list](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L38).   
13) Tell FlockWatch which [email addresses should receive an email](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L41) summarizing the FlockWatch output whenever FlockWatch generates trending and co-occurrence reports.  
    * FlockWatch will try to send this email using a Gmail account. Give FlockWatch the [Gmail account name](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L42) (without "@gmail.com") and the [password](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L43) for that account.  
15) The config parameters about context ([here](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L35) and [here](https://github.com/sjacks26/FlockWatch/blob/deploy/config_template.py#L36)) currently don't affect output. In the future, FlockWatch will be able to provide examples of messages containing trending terms or co-occurrence pairs.   

### Running FlockWatch  

Once you have 

### time_analysis

## Future features

In the future, FlockWatch will:
1) give the user some in-context examples of the trending terms and co-occurrence pairs that it reports
2) have more robust error handling and more informative error messages
 
## Requirements

FlockWatch was developed and tested using:  
* [Python 3.6](https://www.python.org/downloads/release/python-364/)  
* [nltk 3.2.5](https://pypi.org/project/nltk/3.2.5/) (including the English stopwords list, which is [installed separately](https://stackoverflow.com/questions/41610543/corpora-stopwords-not-found-when-import-nltk-library))  
* [pandas 0.22.0](https://pypi.org/project/pandas/0.22.0/)  
* [scipy 1.0.0](https://pypi.org/project/scipy/1.0.0/)  
* [pymongo 3.4.0](https://pypi.org/project/pymongo/3.4.0/)  
* [matplotlib 2.1.2](https://pypi.org/project/matplotlib/2.1.2/)  

FlockWatch.py is the primary code for FlockWatch.  
time_analysis/time_analysis.py is another supplemental file that looks out the output of co-occurrences over time.  
