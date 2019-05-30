# DEI-Topos-Assignment
# Assignment given by Topos
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Created on Wed May 22 16:30:57 2019

@author: saigopalpolsani
"""

#We import the libraries we are going to use
import numpy as np
import pandas as pd
import re
import requests
import urllib.request
import time
from bs4 import BeautifulSoup

#Now we get the content from the webpage
url='https://en.wikipedia.org/wiki/List_of_United_States_cities_by_population'
res=requests.get(url)
res
soup = BeautifulSoup(res.content,'html')
soup
Mytable = soup.find('table',{'class':'wikitable sortable'})
Mytable
links=Mytable.find_all('a')
links
Cities=[]
for i in links:
    Cities.append(i.get('title'))
    
Cities
City=[]
for j in Cities:
    if j!= None:
        City.append(j)
City

df=pd.DataFrame()
df['City']=City

# Dropping unnecessary rows and columns
newdf=df['City'].drop(df.index[[9,21,26,39,42,48,54,60,65,70,98,102,131,134,142,157,175,182,196,220,243,307,320]]).reset_index()
del newdf['index']
City
n1=newdf.City.str.split(",",n=1,expand=True)
newdf['City']=n1[0]
newdf['State']=n1[1]

# Filling the missing states
newdf.loc[53,'State']=newdf.loc[54,'City']
newdf.loc[24,'State']=newdf.loc[25,'City']
newdf.loc[0,'State']=newdf.loc[1,'City']
newdf.loc[[2,12,18],'State']='California'
newdf.loc[4,'State']='Illinois'
newdf.loc[[6,11,13],'State']='Texas'
newdf.loc[[9,85],'State']='Pennsylvania'
newdf.loc[21,'State']='Indiana'
newdf.loc[26,'State']='Colorado'
newdf.loc[30,'State']='Massachusetts'
newdf.loc[33,'State']='Michigan'
newdf.loc[38,'State']='Oklahoma'
newdf.loc[40,'State']='Nevada'
newdf.loc[43,'State']='Maryland'
newdf.loc[45,'State']='Wisconsin'
newdf.loc[59,'State']='Florida'
newdf.loc[62,'State']='Minnesota'
newdf.loc[66,'State']='Louisiana'
newdf.loc[[70,86],'State']='Ohio'
newdf.loc[75,'State']='Hawaii'
newdf.loc[82,'State']='Missouri'
newdf.loc[136,'State']='Utah'

# Dropping Null values and removing columns which are not needed
citydf=newdf
citydf.dropna(inplace = True)
ndf=citydf.reset_index()
cdf=ndf.drop(ndf.index[20]).reset_index()
del cdf['index']
del cdf['level_0']
cdf.index
cdf.index +=1
cdf.index

# Getting the information contained within specific tags
al = soup.find_all('a')
for l in al:
    print(l.get('href'))

    

rows = soup.find_all('tr')
print(rows)    

for r in rows:
    rtd = r.find_all('td')
print(rtd)
type(rtd)

# Removing HTML tags using regular expressions

lrows = []
for row in rows:
    c = row.find_all('td')
    sc = str(c)
    clean = re.compile('<.*?>')
    c2 = (re.sub(clean, '',sc))
    lrows.append(c2)
print(c2)
type(c2)

# Creating a dataframe with cleaned data (specific and clear rows and columns)
df1=pd.DataFrame(lrows)
df1
df2=df1.loc[20:]
df2

df3 = df2[0].str.split('\n', expand=True)
df3
df3[0]=df3[0].str.strip('[')
del df3[11]
df3=df3.loc[:330].reset_index()

del df3['index']

df3[1]=df3[1].str.strip(',')
df3[2]=df3[2].str.strip(',')
df3[3]=df3[3].str.lstrip(',')
df3[4]=df3[4].str.lstrip(',')
df3[5]=df3[5].str.lstrip(',')
df3[6]=df3[6].str.lstrip(',')
df3[7]=df3[7].str.lstrip(',')
df3[8]=df3[8].str.lstrip(',')
df3[9]=df3[9].str.lstrip(',')
df3[10]=df3[10].str.lstrip(',')

df3.columns=['2017 rank','City','State','2017 estimate','2010 census','Change','2016 Land Area (sq mil)','2016 Land Area (km sq)','2016 Population Density (sq mil)','2016 Population Density (km sq)','Location']
df3=df3.applymap(lambda r: r.strip())

# Considering only the 10 most populous cities and creating a new dataframe
df4=df3.loc[0:9]

# Adding new columns to the dataframe which describes more about the city
df4['Time zone']=['UTC−05:00 (EST)','UTC−08:00 (Pacific)','UTC−06:00 (Central)','UTC−6 (CST)','UTC−7 (MST)','UTC-5 (EST)','UTC−6 (CST)','UTC−8 (Pacific)','UTC−6 (Central)','UTC−8 (Pacific Time Zone)']
df4['Time zone Summer (DST)']=['UTC−04:00 (EDT)','UTC−07:00 (PDT)','UTC−05:00 (Central)','UTC−5 (CDT)','No DST','UTC-4 (EDT)','UTC−5 (CDT)','UTC−7 (PDT)','UTC−5 (Central)','UTC−7 (Pacific Daylight Time)']
df4['Area Codes']=['212/646/332, 718/347/929, 917','213/323, 310/424, 747/818','312/872 and 773/872','713, 281, 832, 346','East: 480,Central: 602,West: 623','215, 267, 445','210 (majority), 830 (portions), 726','619, 858','214, 469, 972, 682, 817','408/669']
df4['Website']=['NYC.gov','lacity.org','cityofchicago.org','houstontx.gov','phoenix.gov','phila.gov','SanAntonio.gov','SanDiego.gov','dallascityhall.com','sanjose.gov']

# Converting the dataframes into csv files
df3.to_csv("USCities.csv")
df4.to_csv("USCitiesnew.csv")
