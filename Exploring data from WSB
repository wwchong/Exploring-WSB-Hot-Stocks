from psaw import PushshiftAPI
import datetime
import matplotlib.pyplot as plt
from datetime import timedelta
import yfinance as yf
import pandas as pd
import numpy as np
import random
import seaborn as sns

api = PushshiftAPI()

#getting all titles of wallstreetbets community for the past month and save them in list1
list1 = []
start_time = int((datetime.datetime.today()+timedelta(-30)).timestamp())
submission = list(api.search_submissions(after=start_time,subreddit='wallstreetbets',filter=['url','author','title']))
    
for sub in submission:
    words = sub.title.split()
    for characters in words:
        list1.append(characters)

#Saving list1 to wsb.csv
data = pd.DataFrame(list1)
data.to_csv("wsb.csv")

#check if the words in the list1 contains dollar sign which indicates a ticker name
list2 = []
isalpha = False
for name in list1:
    if name.startswith("$"):
        temp = 1
        i = temp
        for k in range(len(name)):
            try:
                if name[i].isalpha():
                    i += 1
                else:
                    temp += 1
                    i = temp
            except IndexError:
                if i==len(name):
                    list2.append(name[temp:])
                else:
                    list2.append(name[temp:i])
                continue
list3 = list(set(filter(lambda p: len(p)>0,list2)))           

#check if the words in the list3 are really stock tickers and save the confirmed tickers in list4
start = datetime.datetime.today() + timedelta(-30)
end = datetime.datetime.today()
list4 = []
for name in list3:
    if name.upper() not in list4:
        temp = name.upper()
        ticker = yf.Ticker(temp)
        training_data= ticker.history(start=start,end=end,interval='5m')
        if len(training_data)>0:
            list4.append(temp)
    else:
        list4.append(name.upper())
        
#Calculating frequency of tickers in list4 and save the result in dict1
dict1 = dict()
for j in list4:
    if j in dict1.keys():
        dict1[j] += 1
    else:
        dict1[j] = 1

#Sorting the data according to their frequency and we will only look at the top 20 tickers
dict2 = {k: v for k, v in sorted(dict1.items(), key=lambda item: item[1],reverse=True)}
data3 = pd.DataFrame(data={"Ticker":dict2.keys(),"Frequency":dict2.values()},index=np.arange(0,len(dict2.keys())))
data4 = data3[:20]
data4 = data4.sample(frac=1)
data4.reset_index(inplace=True)

#Using matplotlib to create scatter plot of the data
plt.figure(figsize=(20,12))

for i in range(len(data4)):
    random.seed(1)
    r = random.random()
    b = random.random()
    g = random.random()
    color = (r, g, b)
    plt.scatter(x=i,y=data4.iloc[i,2],s=data4.iloc[i,2]*800,color=color,
        cmap=plt.get_cmap('Spectral'))

    label = data4.iloc[i,1]
    x = i
    y = data4.iloc[i,2]
    plt.annotate(
        label,
        xy=(x, y), xytext=(0, 0),
        textcoords="offset points",ha='center',color='black',fontsize=18)
        #bbox=dict(boxstyle='round',pad=0.5, fc='white', alpha=0.5))

plt.title("Frequency of each sticker appeared in the Wallstreebets for the past 30 days",fontsize=18)
plt.ylabel("Frequency",fontsize=18)
plt.show()
