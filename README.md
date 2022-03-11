# Data-Analysis
>>> import numpy as np
>>> import pandas as pd
>>> import matplotlib.pyplot as plt
>>> import seaborn as sns
>>> import folium as fo
>>> import geopandas as gpd
>>> import re
>>> import sklearn.metrics as sm
>>> from branca.element import Figure
>>> from folium.plugins import TimeSliderChoropleth
>>> from sklearn.model_selection import train_test_split as tts
>>> from sklearn import preprocessing as pp, linear_model as lm
>>> df_covid = pd.read_csv(r'D:\BSC. HON\Online\India_covid_data.csv')
>>> india_geojson=gpd.read_file(r'D:\BSC. HON\Online\India_geojson.json')
>>> df_covid['Death'] = df_covid['Death'].astype(int)
>>> df_covid['Date']=pd.to_datetime(df_covid['Date']).apply(lambda x: x - pd.DateOffset(days=1))
>>> id_dict={'Andaman and Nicobar Islands': '0', 'Arunachal Pradesh': '1', 'Assam': '2', 'Bihar': '3', 'Chandigarh': '4', 'Chhattisgarh': '5', 'Dadra and Nagar Haveli and Daman and Diu': '6', 'Daman and Diu': '7', 'Goa': '8', 'Gujarat': '9', 'Haryana': '10', 'Himachal Pradesh': '11', 'Jharkhand': '12', 'Karnataka': '13', 'Kerala': '14', 'Lakshadweep': '15', 'Madhya Pradesh': '16', 'Maharashtra': '17', 'Manipur': '18', 'Meghalaya': '19', 'Mizoram': '20', 'Nagaland': '21', 'Delhi': '22', 'Puducherry': '23', 'Punjab': '24', 'Rajasthan': '25', 'Sikkim': '26', 'Tamil Nadu': '27', 'Telangana': '28', 'Tripura': '29', 'Uttar Pradesh': '30', 'Uttarakhand': '31', 'West Bengal': '32', 'Odisha': '33', 'Andhra Pradesh': '34', 'Jammu and Kashmir': '35', 'Union Territory of Ladakh': '36'}
>>> df_covid['state_id']=df_covid['Name of State / UT'].map(id_dict)
>>> df_covid['ActiveCases']=df_covid['Total Confirmed cases']-(df_covid['Cured/Discharged/Migrated']+df_covid['Death'])
>>> bins = np.linspace(min(df_covid['ActiveCases']), max(df_covid['ActiveCases']), 11)
>>> df_covid['color']=pd.cut(df_covid['ActiveCases'],bins,labels=['#FFEBEB','#F8D2D4','#F2B9BE','#EBA1A8','#E58892','#DE6F7C','#D85766','#D13E50','#CB253A','#C50D24'],include_lowest=False)
>>> df_covid=df_covid[['Date','state_id','color']]
>>> for date in df_covid['Date'].unique():
...     diff=set([str(i) for i in range(37)])-set(df_covid[df_covid['Date']==date]['state_id'])
...     for i in diff:
...         df_covid=pd.concat([df_covid,pd.DataFrame([[date,'#0073CF',i]],columns=['Date','color','state_id'])],ignore_index=True)
...         df_covid.sort_values('Date',inplace=True)
... 
>>> df_covid['Date'] = pd.to_datetime(df_covid['Date'])
>>> df_covid['Date']=(df_covid['Date'].astype('int64')// 10**9).astype('U10')
>>> df_covid['Date']=df_covid['Date'].astype('int64')
>>> df_covid['state_id'] = df_covid['state_id'].astype(str)
>>> df_covid['color'] = df_covid['color'].astype(str)
>>> covid_dict={}
>>> for i in df_covid['state_id'].unique():
...     covid_dict[i]={}
...     for j in df_covid[df_covid['state_id']==i].set_index(['state_id']).values:
...         covid_dict[i][j[0]]={'color':j[1],'opacity':0.6}
... 
>>> india_geojson['state_id'] = india_geojson['st_nm'].map(id_dict)
>>> india_geojson.drop(columns='st_nm', inplace=True)
>>> df =india_geojson.set_index('state_id')
>>> fig6=Figure(height=850,width=1000)
>>> map_cov = fo.Map(location=[24, 84], tiles='cartodbpositron', zoom_start=5)
>>> fig6.add_child(map_cov)
<branca.element.Figure object at 0x0000022399A7FCD0>
>>> g = TimeSliderChoropleth(df.to_json(), styledict=covid_dict).add_to(map_cov)
>>> map_cov.save(r'D:\BSC. HON\Online\india_covid_map.html')
![image](https://user-images.githubusercontent.com/70702006/157858815-efcc9815-abf8-46ca-902a-5e79991c90aa.png)
