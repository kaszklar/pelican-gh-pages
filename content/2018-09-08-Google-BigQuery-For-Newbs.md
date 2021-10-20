Title: Google Big Query For Newbs (By a Newb)
Date: 2018-09-08
Category: how tos
Status: published

> BigQuery is a RESTful web service that enables interactive analysis of massively large datasets working in conjunction with Google Storage.  
-[Wikipedia](https://en.wikipedia.org/wiki/BigQuery)

Up until my final project at Metis I had always downloaded csv's and then, if they were sizable, used them to build out my own tables and database to work with. However, for this final passion project, I really wanted to explore the Global Fishing Watch data. After requesting access, I learned that it was exclusively available on Google Big Query - needless to say I was intimidated! GBQ, as I'll now refer to it, is just one member of an entire host of cloud computing services. In order to work with the data that I wanted, I needed to navigate that world - a process that was painstaking at times. I've written this blog post to assist other fledgling data scientists.

## Part I: Downloading Results 
In GBQ, once you have access to some tables, it isn't hard to figure out how to query them. What I struggled with was downloading the results to play with!

![too big]({static}/images/gbq/too-big.png)

When I tried to download them, I was told that the results were too big. I don't know what the cut off is, but this error message was received from a 3.74MB result.

In order to get around this, you will need to:

* create a project
* create a dataset
* copy the original tables into your dataset, OR query the original data and save those results as tables in your dataset

You can create a project using the main [Google Cloud Console Dashboard](https://console.cloud.google.com/home/dashboard).

![new project]({static}/images/gbq/new-project.png)


GBQ is giving away 1 terabyte (or $300 worth) of free usage to new users for up to 365 days. You should see that at the top of your browser window. Unfortunately, you will still need to [set up a billing account](https://cloud.google.com/billing/docs/how-to/modify-project) for your new project. Google was very explicit that you won't receive any surprise bills; they will notify you when your trial is over and you are about to be charged.

After creating a project, you will need to create a "dataset" in that project before being able to copy any tables into it. I spent much more time than I care to admit searching around and around google cloud before figuring out how to do this. You can accomplish this task by clicking on the little menu expansion arrow next to your project name, directly in GBQ.

![new dataset]({static}/images/gbq/new-dataset.png)

Now, you can copy complete tables or queries into _your_ dataset, then download (it will, however, come in chunks). This is how I first approached the issue, but later moved on to importing my queries directly into a jupyter notebook. 

## Part II: Jupyter Notebook
If you're like me and you do most of your work in notebooks, there is a way to import your queries directly into a dataframe using a few libraries. As I work in python, this section will be a bit specific to that language.
Before we even open jupyter though, we will need to make sure google knows that we are authorized. This process also took me quite a while, and I suspect that there may be a more elegant way to do this. Generally speaking, you will need to 
* create and download credentials and then 
* authenticate yourself in your notebook.
  
### Credentials
Do do this, you will need to create a service account. It allows you to grant access to many people and set permission levels (even if that person is just yourself 😛 ) You can do this on the [service account key page](https://console.cloud.google.com/apis/credentials/serviceaccountkey). The instructions below come from [here](https://cloud.google.com/docs/authentication/production).

1. Go to the Create service account key page in the GCP Console.
2. From the Service account drop-down list, select New service account.
3. Enter a name into the Service account name field. (I chose my own name 💁)
4. From the Role drop-down list, select Project > Owner. Chose the JSON key type.
5. Click Create. A JSON file that contains your key downloads to your computer.

Now save that file & don't lose it! If you aren't familiar with using keys, this is a unique identifier for _you_ so it is best kept private. Often times these types of files cannot be downloaded again. This one is particular to your google account _and_ your specific dataset.

### Notebooks
You can do this process on your local machine or any cloud instance. I suggest working in the cloud because you _won't_ be warned if your query can't be fully stored in local memory... you will just recieve a subset of your query.

You will need to install the following libraries: 

```pip install google-auth```    
```pip install pandas-gbq -U```   

Import them:  
```python
from google.oauth2 import service_account    
from pandas.io import gbq
```

Credential yourself:  
```python
   credentials = service_account.Credentials.from_service_account_file(
   '/path/to/your_credentials.json'
```

Define your query as a string using triple quotes for readability:  
```python
query="""
SELECT 
   *
FROM 
   YOUR_DATASET.YOUR_TABLE
"""
```
Now run your query and assign it to a dataframe:  
```python
df = gbq.read_gbq(query, project_id='YOUR_PROJECT_ID', dialect ='standard')
```

The first time you do this, you will be prompted to confirm your google account. After you run the above, you will be prompted to click on a link, log in, retrieve a key, and input that into your notebook. Once you do that all future queries will run smoothly.   


🎉 You are now playing with fire! Time to analyze. 

As I mentioned above, this was a trial and error process for me. I want to thank my good friend [Anahita](http://www.twitter.com/anahitabahri) for her invaluable assistance, as well as for her moral support in my final project.  
If you have any suggestions for improving this process, feel free to reach out!

