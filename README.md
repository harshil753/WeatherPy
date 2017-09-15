
# WeatherPy

## Observations

- The temperature increases the closer we get to the equator/ latitude 0
- Latitude has no impact on cloudiness or wind speed
- It becomes less humid the closer to the equator you are


```python
import pandas as pd
import requests as req
from citipy import citipy 
import matplotlib.pyplot as plt
import seaborn as sns
import random
import time as time
pd.options.mode.chained_assignment = None
key="2316b03031cfb536c195835482e8bc0c"
```


```python
#create list of random latitudes and longitudes
data= pd.DataFrame(columns =['Lat',"Lng","City","Temperature","Humidity","Clouds","Wind Speed"])
lat = []
lng = []
for x in range(0,1200):
    lat.append(random.uniform(-90,91))
    lng.append(random.uniform(-181,181))
data['Lat']=lat
data['Lng']=lng
data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lng</th>
      <th>City</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>87.097237</td>
      <td>52.248082</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-70.662926</td>
      <td>-11.955065</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-83.341232</td>
      <td>113.235407</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>58.523751</td>
      <td>-96.934863</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>72.239971</td>
      <td>-71.609458</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
#find the cities related to the coordinates
cities =[]
for index, row in data.iterrows():
    city=citipy.nearest_city(row["Lat"],row["Lng"])
    cities.append(city.city_name)
data['City']=cities
data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lng</th>
      <th>City</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>87.097237</td>
      <td>52.248082</td>
      <td>belushya guba</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-70.662926</td>
      <td>-11.955065</td>
      <td>cape town</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-83.341232</td>
      <td>113.235407</td>
      <td>albany</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>58.523751</td>
      <td>-96.934863</td>
      <td>thompson</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>72.239971</td>
      <td>-71.609458</td>
      <td>clyde river</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
#check for and remove duplicates and make sure there are atleast 500 unique cities to test
new_data = data.drop_duplicates("City",keep="first")
len(new_data)
```




    508




```python
#create empty arrays to store the data for temperature, humidity, clouds, and wind speed 
temp = []
humidity =[]
clouds = []
wind = []

counter = 0
url = "https://api.openweathermap.org/data/2.5/weather?q="
units = "imperial"

#iterate through the rows to pull data from the api 
for index, row in new_data.iterrows():
    counter +=1
    city = row["City"]
    target_url = url+city+"&appid="+key+"&units="+units
    print("we are now on city number "+str(counter))
    print("The name of the city is "+row["City"])
    print(target_url)
    print("__________________________________________________________________________________________")
    info = req.get(target_url).json()
    temp.append(info['main']['temp'])
    humidity.append(info['main']['humidity'])
    clouds.append(info['clouds']['all'])
    wind.append(info['wind']['speed'])
    time.sleep(1)
#assign the values from the arrays to the data frame
new_data["Temperature"]=temp
new_data["Humidity"]=humidity
new_data["Clouds"]=clouds
new_data["Wind Speed"]=wind
```

    we are now on city number 1
    The name of the city is belushya guba
    https://api.openweathermap.org/data/2.5/weather?q=belushya guba&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 2
    The name of the city is cape town
    https://api.openweathermap.org/data/2.5/weather?q=cape town&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 3
    The name of the city is albany
    https://api.openweathermap.org/data/2.5/weather?q=albany&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 4
    The name of the city is thompson
    https://api.openweathermap.org/data/2.5/weather?q=thompson&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 5
    The name of the city is clyde river
    https://api.openweathermap.org/data/2.5/weather?q=clyde river&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 6
    The name of the city is tarudant
    https://api.openweathermap.org/data/2.5/weather?q=tarudant&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 7
    The name of the city is melfi
    https://api.openweathermap.org/data/2.5/weather?q=melfi&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 8
    The name of the city is mahebourg
    https://api.openweathermap.org/data/2.5/weather?q=mahebourg&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 9
    The name of the city is bambanglipuro
    https://api.openweathermap.org/data/2.5/weather?q=bambanglipuro&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 10
    The name of the city is qaanaaq
    https://api.openweathermap.org/data/2.5/weather?q=qaanaaq&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 11
    The name of the city is lavrentiya
    https://api.openweathermap.org/data/2.5/weather?q=lavrentiya&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 12
    The name of the city is bambous virieux
    https://api.openweathermap.org/data/2.5/weather?q=bambous virieux&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 13
    The name of the city is illoqqortoormiut
    https://api.openweathermap.org/data/2.5/weather?q=illoqqortoormiut&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 14
    The name of the city is georgetown
    https://api.openweathermap.org/data/2.5/weather?q=georgetown&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 15
    The name of the city is hermanus
    https://api.openweathermap.org/data/2.5/weather?q=hermanus&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 16
    The name of the city is ercis
    https://api.openweathermap.org/data/2.5/weather?q=ercis&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 17
    The name of the city is pitimbu
    https://api.openweathermap.org/data/2.5/weather?q=pitimbu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 18
    The name of the city is avarua
    https://api.openweathermap.org/data/2.5/weather?q=avarua&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 19
    The name of the city is port elizabeth
    https://api.openweathermap.org/data/2.5/weather?q=port elizabeth&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 20
    The name of the city is new norfolk
    https://api.openweathermap.org/data/2.5/weather?q=new norfolk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 21
    The name of the city is jamestown
    https://api.openweathermap.org/data/2.5/weather?q=jamestown&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 22
    The name of the city is barrow
    https://api.openweathermap.org/data/2.5/weather?q=barrow&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 23
    The name of the city is busselton
    https://api.openweathermap.org/data/2.5/weather?q=busselton&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 24
    The name of the city is cheuskiny
    https://api.openweathermap.org/data/2.5/weather?q=cheuskiny&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 25
    The name of the city is torbay
    https://api.openweathermap.org/data/2.5/weather?q=torbay&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 26
    The name of the city is nome
    https://api.openweathermap.org/data/2.5/weather?q=nome&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 27
    The name of the city is safonovo
    https://api.openweathermap.org/data/2.5/weather?q=safonovo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 28
    The name of the city is kodiak
    https://api.openweathermap.org/data/2.5/weather?q=kodiak&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 29
    The name of the city is ushuaia
    https://api.openweathermap.org/data/2.5/weather?q=ushuaia&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 30
    The name of the city is beringovskiy
    https://api.openweathermap.org/data/2.5/weather?q=beringovskiy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 31
    The name of the city is magdagachi
    https://api.openweathermap.org/data/2.5/weather?q=magdagachi&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 32
    The name of the city is sao filipe
    https://api.openweathermap.org/data/2.5/weather?q=sao filipe&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 33
    The name of the city is amos
    https://api.openweathermap.org/data/2.5/weather?q=amos&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 34
    The name of the city is cidreira
    https://api.openweathermap.org/data/2.5/weather?q=cidreira&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 35
    The name of the city is quatre cocos
    https://api.openweathermap.org/data/2.5/weather?q=quatre cocos&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 36
    The name of the city is coihaique
    https://api.openweathermap.org/data/2.5/weather?q=coihaique&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 37
    The name of the city is lakes entrance
    https://api.openweathermap.org/data/2.5/weather?q=lakes entrance&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 38
    The name of the city is kapaa
    https://api.openweathermap.org/data/2.5/weather?q=kapaa&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 39
    The name of the city is lompoc
    https://api.openweathermap.org/data/2.5/weather?q=lompoc&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 40
    The name of the city is rikitea
    https://api.openweathermap.org/data/2.5/weather?q=rikitea&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 41
    The name of the city is caruray
    https://api.openweathermap.org/data/2.5/weather?q=caruray&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 42
    The name of the city is hilo
    https://api.openweathermap.org/data/2.5/weather?q=hilo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 43
    The name of the city is arraial do cabo
    https://api.openweathermap.org/data/2.5/weather?q=arraial do cabo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 44
    The name of the city is puerto ayora
    https://api.openweathermap.org/data/2.5/weather?q=puerto ayora&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 45
    The name of the city is sitka
    https://api.openweathermap.org/data/2.5/weather?q=sitka&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 46
    The name of the city is bredasdorp
    https://api.openweathermap.org/data/2.5/weather?q=bredasdorp&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 47
    The name of the city is christchurch
    https://api.openweathermap.org/data/2.5/weather?q=christchurch&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 48
    The name of the city is yellowknife
    https://api.openweathermap.org/data/2.5/weather?q=yellowknife&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 49
    The name of the city is luderitz
    https://api.openweathermap.org/data/2.5/weather?q=luderitz&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 50
    The name of the city is tiznit
    https://api.openweathermap.org/data/2.5/weather?q=tiznit&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 51
    The name of the city is mpophomeni
    https://api.openweathermap.org/data/2.5/weather?q=mpophomeni&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 52
    The name of the city is linjiang
    https://api.openweathermap.org/data/2.5/weather?q=linjiang&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 53
    The name of the city is lebu
    https://api.openweathermap.org/data/2.5/weather?q=lebu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 54
    The name of the city is attawapiskat
    https://api.openweathermap.org/data/2.5/weather?q=attawapiskat&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 55
    The name of the city is vyartsilya
    https://api.openweathermap.org/data/2.5/weather?q=vyartsilya&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 56
    The name of the city is barentsburg
    https://api.openweathermap.org/data/2.5/weather?q=barentsburg&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 57
    The name of the city is beyneu
    https://api.openweathermap.org/data/2.5/weather?q=beyneu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 58
    The name of the city is constitucion
    https://api.openweathermap.org/data/2.5/weather?q=constitucion&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 59
    The name of the city is chokurdakh
    https://api.openweathermap.org/data/2.5/weather?q=chokurdakh&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 60
    The name of the city is tasiilaq
    https://api.openweathermap.org/data/2.5/weather?q=tasiilaq&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 61
    The name of the city is isangel
    https://api.openweathermap.org/data/2.5/weather?q=isangel&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 62
    The name of the city is saint-augustin
    https://api.openweathermap.org/data/2.5/weather?q=saint-augustin&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 63
    The name of the city is kruisfontein
    https://api.openweathermap.org/data/2.5/weather?q=kruisfontein&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 64
    The name of the city is bluff
    https://api.openweathermap.org/data/2.5/weather?q=bluff&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 65
    The name of the city is santa eulalia del rio
    https://api.openweathermap.org/data/2.5/weather?q=santa eulalia del rio&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 66
    The name of the city is alihe
    https://api.openweathermap.org/data/2.5/weather?q=alihe&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 67
    The name of the city is alice springs
    https://api.openweathermap.org/data/2.5/weather?q=alice springs&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 68
    The name of the city is amderma
    https://api.openweathermap.org/data/2.5/weather?q=amderma&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 69
    The name of the city is hofn
    https://api.openweathermap.org/data/2.5/weather?q=hofn&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 70
    The name of the city is tiksi
    https://api.openweathermap.org/data/2.5/weather?q=tiksi&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 71
    The name of the city is fortuna
    https://api.openweathermap.org/data/2.5/weather?q=fortuna&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 72
    The name of the city is duzce
    https://api.openweathermap.org/data/2.5/weather?q=duzce&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 73
    The name of the city is khatanga
    https://api.openweathermap.org/data/2.5/weather?q=khatanga&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 74
    The name of the city is moussoro
    https://api.openweathermap.org/data/2.5/weather?q=moussoro&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 75
    The name of the city is nanakuli
    https://api.openweathermap.org/data/2.5/weather?q=nanakuli&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 76
    The name of the city is ponta do sol
    https://api.openweathermap.org/data/2.5/weather?q=ponta do sol&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 77
    The name of the city is wakema
    https://api.openweathermap.org/data/2.5/weather?q=wakema&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 78
    The name of the city is phangnga
    https://api.openweathermap.org/data/2.5/weather?q=phangnga&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 79
    The name of the city is chumikan
    https://api.openweathermap.org/data/2.5/weather?q=chumikan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 80
    The name of the city is hobart
    https://api.openweathermap.org/data/2.5/weather?q=hobart&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 81
    The name of the city is nenjiang
    https://api.openweathermap.org/data/2.5/weather?q=nenjiang&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 82
    The name of the city is chuy
    https://api.openweathermap.org/data/2.5/weather?q=chuy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 83
    The name of the city is souillac
    https://api.openweathermap.org/data/2.5/weather?q=souillac&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 84
    The name of the city is mar del plata
    https://api.openweathermap.org/data/2.5/weather?q=mar del plata&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 85
    The name of the city is tuktoyaktuk
    https://api.openweathermap.org/data/2.5/weather?q=tuktoyaktuk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 86
    The name of the city is naze
    https://api.openweathermap.org/data/2.5/weather?q=naze&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 87
    The name of the city is saskylakh
    https://api.openweathermap.org/data/2.5/weather?q=saskylakh&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 88
    The name of the city is bathsheba
    https://api.openweathermap.org/data/2.5/weather?q=bathsheba&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 89
    The name of the city is atuona
    https://api.openweathermap.org/data/2.5/weather?q=atuona&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 90
    The name of the city is marcona
    https://api.openweathermap.org/data/2.5/weather?q=marcona&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 91
    The name of the city is pleasanton
    https://api.openweathermap.org/data/2.5/weather?q=pleasanton&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 92
    The name of the city is saint george
    https://api.openweathermap.org/data/2.5/weather?q=saint george&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 93
    The name of the city is guerrero
    https://api.openweathermap.org/data/2.5/weather?q=guerrero&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 94
    The name of the city is jackson
    https://api.openweathermap.org/data/2.5/weather?q=jackson&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 95
    The name of the city is hithadhoo
    https://api.openweathermap.org/data/2.5/weather?q=hithadhoo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 96
    The name of the city is vondrozo
    https://api.openweathermap.org/data/2.5/weather?q=vondrozo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 97
    The name of the city is punta arenas
    https://api.openweathermap.org/data/2.5/weather?q=punta arenas&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 98
    The name of the city is raudeberg
    https://api.openweathermap.org/data/2.5/weather?q=raudeberg&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 99
    The name of the city is severo-kurilsk
    https://api.openweathermap.org/data/2.5/weather?q=severo-kurilsk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 100
    The name of the city is mataura
    https://api.openweathermap.org/data/2.5/weather?q=mataura&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 101
    The name of the city is baghdad
    https://api.openweathermap.org/data/2.5/weather?q=baghdad&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 102
    The name of the city is bethel
    https://api.openweathermap.org/data/2.5/weather?q=bethel&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 103
    The name of the city is san policarpo
    https://api.openweathermap.org/data/2.5/weather?q=san policarpo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 104
    The name of the city is tonstad
    https://api.openweathermap.org/data/2.5/weather?q=tonstad&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 105
    The name of the city is sibolga
    https://api.openweathermap.org/data/2.5/weather?q=sibolga&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 106
    The name of the city is bengkulu
    https://api.openweathermap.org/data/2.5/weather?q=bengkulu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 107
    The name of the city is padang
    https://api.openweathermap.org/data/2.5/weather?q=padang&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 108
    The name of the city is korfovskiy
    https://api.openweathermap.org/data/2.5/weather?q=korfovskiy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 109
    The name of the city is rawannawi
    https://api.openweathermap.org/data/2.5/weather?q=rawannawi&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 110
    The name of the city is iqaluit
    https://api.openweathermap.org/data/2.5/weather?q=iqaluit&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 111
    The name of the city is blonduos
    https://api.openweathermap.org/data/2.5/weather?q=blonduos&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 112
    The name of the city is ahipara
    https://api.openweathermap.org/data/2.5/weather?q=ahipara&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 113
    The name of the city is khani
    https://api.openweathermap.org/data/2.5/weather?q=khani&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 114
    The name of the city is dingle
    https://api.openweathermap.org/data/2.5/weather?q=dingle&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 115
    The name of the city is codrington
    https://api.openweathermap.org/data/2.5/weather?q=codrington&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 116
    The name of the city is aykhal
    https://api.openweathermap.org/data/2.5/weather?q=aykhal&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 117
    The name of the city is lashio
    https://api.openweathermap.org/data/2.5/weather?q=lashio&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 118
    The name of the city is carnarvon
    https://api.openweathermap.org/data/2.5/weather?q=carnarvon&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 119
    The name of the city is upernavik
    https://api.openweathermap.org/data/2.5/weather?q=upernavik&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 120
    The name of the city is mercedes
    https://api.openweathermap.org/data/2.5/weather?q=mercedes&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 121
    The name of the city is srednekolymsk
    https://api.openweathermap.org/data/2.5/weather?q=srednekolymsk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 122
    The name of the city is east london
    https://api.openweathermap.org/data/2.5/weather?q=east london&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 123
    The name of the city is doha
    https://api.openweathermap.org/data/2.5/weather?q=doha&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 124
    The name of the city is cockburn harbour
    https://api.openweathermap.org/data/2.5/weather?q=cockburn harbour&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 125
    The name of the city is klaksvik
    https://api.openweathermap.org/data/2.5/weather?q=klaksvik&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 126
    The name of the city is vaini
    https://api.openweathermap.org/data/2.5/weather?q=vaini&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 127
    The name of the city is port hardy
    https://api.openweathermap.org/data/2.5/weather?q=port hardy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 128
    The name of the city is port alfred
    https://api.openweathermap.org/data/2.5/weather?q=port alfred&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 129
    The name of the city is zhezkazgan
    https://api.openweathermap.org/data/2.5/weather?q=zhezkazgan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 130
    The name of the city is jijiga
    https://api.openweathermap.org/data/2.5/weather?q=jijiga&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 131
    The name of the city is dongkan
    https://api.openweathermap.org/data/2.5/weather?q=dongkan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 132
    The name of the city is aripuana
    https://api.openweathermap.org/data/2.5/weather?q=aripuana&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 133
    The name of the city is emilio carranza
    https://api.openweathermap.org/data/2.5/weather?q=emilio carranza&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 134
    The name of the city is butaritari
    https://api.openweathermap.org/data/2.5/weather?q=butaritari&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 135
    The name of the city is domoni
    https://api.openweathermap.org/data/2.5/weather?q=domoni&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 136
    The name of the city is kawana waters
    https://api.openweathermap.org/data/2.5/weather?q=kawana waters&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 137
    The name of the city is havelock
    https://api.openweathermap.org/data/2.5/weather?q=havelock&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 138
    The name of the city is castro
    https://api.openweathermap.org/data/2.5/weather?q=castro&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 139
    The name of the city is pingshan
    https://api.openweathermap.org/data/2.5/weather?q=pingshan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 140
    The name of the city is taolanaro
    https://api.openweathermap.org/data/2.5/weather?q=taolanaro&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 141
    The name of the city is kamenskoye
    https://api.openweathermap.org/data/2.5/weather?q=kamenskoye&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 142
    The name of the city is rocha
    https://api.openweathermap.org/data/2.5/weather?q=rocha&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 143
    The name of the city is eyl
    https://api.openweathermap.org/data/2.5/weather?q=eyl&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 144
    The name of the city is hit
    https://api.openweathermap.org/data/2.5/weather?q=hit&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 145
    The name of the city is simao
    https://api.openweathermap.org/data/2.5/weather?q=simao&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 146
    The name of the city is point fortin
    https://api.openweathermap.org/data/2.5/weather?q=point fortin&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 147
    The name of the city is airai
    https://api.openweathermap.org/data/2.5/weather?q=airai&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 148
    The name of the city is matara
    https://api.openweathermap.org/data/2.5/weather?q=matara&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 149
    The name of the city is necochea
    https://api.openweathermap.org/data/2.5/weather?q=necochea&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 150
    The name of the city is pevek
    https://api.openweathermap.org/data/2.5/weather?q=pevek&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 151
    The name of the city is puerto escondido
    https://api.openweathermap.org/data/2.5/weather?q=puerto escondido&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 152
    The name of the city is longfeng
    https://api.openweathermap.org/data/2.5/weather?q=longfeng&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 153
    The name of the city is sakakah
    https://api.openweathermap.org/data/2.5/weather?q=sakakah&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 154
    The name of the city is kavaratti
    https://api.openweathermap.org/data/2.5/weather?q=kavaratti&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 155
    The name of the city is victoria
    https://api.openweathermap.org/data/2.5/weather?q=victoria&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 156
    The name of the city is pontianak
    https://api.openweathermap.org/data/2.5/weather?q=pontianak&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 157
    The name of the city is mosquera
    https://api.openweathermap.org/data/2.5/weather?q=mosquera&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 158
    The name of the city is russell
    https://api.openweathermap.org/data/2.5/weather?q=russell&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 159
    The name of the city is boa vista
    https://api.openweathermap.org/data/2.5/weather?q=boa vista&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 160
    The name of the city is cayenne
    https://api.openweathermap.org/data/2.5/weather?q=cayenne&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 161
    The name of the city is lahij
    https://api.openweathermap.org/data/2.5/weather?q=lahij&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 162
    The name of the city is sinnamary
    https://api.openweathermap.org/data/2.5/weather?q=sinnamary&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 163
    The name of the city is krasnoarmeysk
    https://api.openweathermap.org/data/2.5/weather?q=krasnoarmeysk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 164
    The name of the city is sambava
    https://api.openweathermap.org/data/2.5/weather?q=sambava&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 165
    The name of the city is pathein
    https://api.openweathermap.org/data/2.5/weather?q=pathein&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 166
    The name of the city is kamenka
    https://api.openweathermap.org/data/2.5/weather?q=kamenka&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 167
    The name of the city is rossland
    https://api.openweathermap.org/data/2.5/weather?q=rossland&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 168
    The name of the city is geraldton
    https://api.openweathermap.org/data/2.5/weather?q=geraldton&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 169
    The name of the city is menongue
    https://api.openweathermap.org/data/2.5/weather?q=menongue&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 170
    The name of the city is tevaitoa
    https://api.openweathermap.org/data/2.5/weather?q=tevaitoa&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 171
    The name of the city is vagur
    https://api.openweathermap.org/data/2.5/weather?q=vagur&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 172
    The name of the city is vanimo
    https://api.openweathermap.org/data/2.5/weather?q=vanimo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 173
    The name of the city is tidore
    https://api.openweathermap.org/data/2.5/weather?q=tidore&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 174
    The name of the city is mayo
    https://api.openweathermap.org/data/2.5/weather?q=mayo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 175
    The name of the city is mount gambier
    https://api.openweathermap.org/data/2.5/weather?q=mount gambier&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 176
    The name of the city is bacong
    https://api.openweathermap.org/data/2.5/weather?q=bacong&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 177
    The name of the city is tura
    https://api.openweathermap.org/data/2.5/weather?q=tura&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 178
    The name of the city is pestovo
    https://api.openweathermap.org/data/2.5/weather?q=pestovo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 179
    The name of the city is nurota
    https://api.openweathermap.org/data/2.5/weather?q=nurota&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 180
    The name of the city is grindavik
    https://api.openweathermap.org/data/2.5/weather?q=grindavik&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 181
    The name of the city is tumannyy
    https://api.openweathermap.org/data/2.5/weather?q=tumannyy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 182
    The name of the city is riachao das neves
    https://api.openweathermap.org/data/2.5/weather?q=riachao das neves&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 183
    The name of the city is teguldet
    https://api.openweathermap.org/data/2.5/weather?q=teguldet&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 184
    The name of the city is lorengau
    https://api.openweathermap.org/data/2.5/weather?q=lorengau&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 185
    The name of the city is ilulissat
    https://api.openweathermap.org/data/2.5/weather?q=ilulissat&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 186
    The name of the city is baraboo
    https://api.openweathermap.org/data/2.5/weather?q=baraboo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 187
    The name of the city is pangnirtung
    https://api.openweathermap.org/data/2.5/weather?q=pangnirtung&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 188
    The name of the city is marawi
    https://api.openweathermap.org/data/2.5/weather?q=marawi&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 189
    The name of the city is muros
    https://api.openweathermap.org/data/2.5/weather?q=muros&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 190
    The name of the city is skalistyy
    https://api.openweathermap.org/data/2.5/weather?q=skalistyy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 191
    The name of the city is dzhusaly
    https://api.openweathermap.org/data/2.5/weather?q=dzhusaly&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 192
    The name of the city is clovis
    https://api.openweathermap.org/data/2.5/weather?q=clovis&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 193
    The name of the city is kholodnyy
    https://api.openweathermap.org/data/2.5/weather?q=kholodnyy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 194
    The name of the city is vista hermosa
    https://api.openweathermap.org/data/2.5/weather?q=vista hermosa&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 195
    The name of the city is viligili
    https://api.openweathermap.org/data/2.5/weather?q=viligili&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 196
    The name of the city is sorong
    https://api.openweathermap.org/data/2.5/weather?q=sorong&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 197
    The name of the city is broome
    https://api.openweathermap.org/data/2.5/weather?q=broome&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 198
    The name of the city is napradea
    https://api.openweathermap.org/data/2.5/weather?q=napradea&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 199
    The name of the city is senanga
    https://api.openweathermap.org/data/2.5/weather?q=senanga&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 200
    The name of the city is gat
    https://api.openweathermap.org/data/2.5/weather?q=gat&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 201
    The name of the city is piacabucu
    https://api.openweathermap.org/data/2.5/weather?q=piacabucu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 202
    The name of the city is muscat
    https://api.openweathermap.org/data/2.5/weather?q=muscat&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 203
    The name of the city is hay river
    https://api.openweathermap.org/data/2.5/weather?q=hay river&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 204
    The name of the city is kushima
    https://api.openweathermap.org/data/2.5/weather?q=kushima&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 205
    The name of the city is tuatapere
    https://api.openweathermap.org/data/2.5/weather?q=tuatapere&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 206
    The name of the city is mnogovershinnyy
    https://api.openweathermap.org/data/2.5/weather?q=mnogovershinnyy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 207
    The name of the city is tupaciguara
    https://api.openweathermap.org/data/2.5/weather?q=tupaciguara&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 208
    The name of the city is saint-jean-de-maurienne
    https://api.openweathermap.org/data/2.5/weather?q=saint-jean-de-maurienne&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 209
    The name of the city is petropavlovsk-kamchatskiy
    https://api.openweathermap.org/data/2.5/weather?q=petropavlovsk-kamchatskiy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 210
    The name of the city is gamba
    https://api.openweathermap.org/data/2.5/weather?q=gamba&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 211
    The name of the city is inta
    https://api.openweathermap.org/data/2.5/weather?q=inta&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 212
    The name of the city is oromocto
    https://api.openweathermap.org/data/2.5/weather?q=oromocto&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 213
    The name of the city is leningradskiy
    https://api.openweathermap.org/data/2.5/weather?q=leningradskiy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 214
    The name of the city is faanui
    https://api.openweathermap.org/data/2.5/weather?q=faanui&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 215
    The name of the city is bac lieu
    https://api.openweathermap.org/data/2.5/weather?q=bac lieu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 216
    The name of the city is kompaniyivka
    https://api.openweathermap.org/data/2.5/weather?q=kompaniyivka&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 217
    The name of the city is bonthe
    https://api.openweathermap.org/data/2.5/weather?q=bonthe&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 218
    The name of the city is baruun-urt
    https://api.openweathermap.org/data/2.5/weather?q=baruun-urt&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 219
    The name of the city is aqtobe
    https://api.openweathermap.org/data/2.5/weather?q=aqtobe&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 220
    The name of the city is buchanan
    https://api.openweathermap.org/data/2.5/weather?q=buchanan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 221
    The name of the city is shelburne
    https://api.openweathermap.org/data/2.5/weather?q=shelburne&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 222
    The name of the city is mattru
    https://api.openweathermap.org/data/2.5/weather?q=mattru&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 223
    The name of the city is bosanski novi
    https://api.openweathermap.org/data/2.5/weather?q=bosanski novi&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 224
    The name of the city is alofi
    https://api.openweathermap.org/data/2.5/weather?q=alofi&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 225
    The name of the city is dicabisagan
    https://api.openweathermap.org/data/2.5/weather?q=dicabisagan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 226
    The name of the city is saint-philippe
    https://api.openweathermap.org/data/2.5/weather?q=saint-philippe&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 227
    The name of the city is chinsali
    https://api.openweathermap.org/data/2.5/weather?q=chinsali&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 228
    The name of the city is kavieng
    https://api.openweathermap.org/data/2.5/weather?q=kavieng&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 229
    The name of the city is moose factory
    https://api.openweathermap.org/data/2.5/weather?q=moose factory&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 230
    The name of the city is tapaua
    https://api.openweathermap.org/data/2.5/weather?q=tapaua&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 231
    The name of the city is tigil
    https://api.openweathermap.org/data/2.5/weather?q=tigil&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 232
    The name of the city is maceio
    https://api.openweathermap.org/data/2.5/weather?q=maceio&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 233
    The name of the city is cancun
    https://api.openweathermap.org/data/2.5/weather?q=cancun&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 234
    The name of the city is linxia
    https://api.openweathermap.org/data/2.5/weather?q=linxia&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 235
    The name of the city is taoudenni
    https://api.openweathermap.org/data/2.5/weather?q=taoudenni&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 236
    The name of the city is sorland
    https://api.openweathermap.org/data/2.5/weather?q=sorland&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 237
    The name of the city is acapulco
    https://api.openweathermap.org/data/2.5/weather?q=acapulco&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 238
    The name of the city is dikson
    https://api.openweathermap.org/data/2.5/weather?q=dikson&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 239
    The name of the city is amboasary
    https://api.openweathermap.org/data/2.5/weather?q=amboasary&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 240
    The name of the city is venado tuerto
    https://api.openweathermap.org/data/2.5/weather?q=venado tuerto&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 241
    The name of the city is samarai
    https://api.openweathermap.org/data/2.5/weather?q=samarai&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 242
    The name of the city is kholtoson
    https://api.openweathermap.org/data/2.5/weather?q=kholtoson&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 243
    The name of the city is zachepylivka
    https://api.openweathermap.org/data/2.5/weather?q=zachepylivka&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 244
    The name of the city is birobidzhan
    https://api.openweathermap.org/data/2.5/weather?q=birobidzhan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 245
    The name of the city is newton
    https://api.openweathermap.org/data/2.5/weather?q=newton&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 246
    The name of the city is norman wells
    https://api.openweathermap.org/data/2.5/weather?q=norman wells&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 247
    The name of the city is grand river south east
    https://api.openweathermap.org/data/2.5/weather?q=grand river south east&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 248
    The name of the city is tahe
    https://api.openweathermap.org/data/2.5/weather?q=tahe&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 249
    The name of the city is comodoro rivadavia
    https://api.openweathermap.org/data/2.5/weather?q=comodoro rivadavia&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 250
    The name of the city is ostrovnoy
    https://api.openweathermap.org/data/2.5/weather?q=ostrovnoy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 251
    The name of the city is olovo
    https://api.openweathermap.org/data/2.5/weather?q=olovo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 252
    The name of the city is znamenskoye
    https://api.openweathermap.org/data/2.5/weather?q=znamenskoye&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 253
    The name of the city is lata
    https://api.openweathermap.org/data/2.5/weather?q=lata&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 254
    The name of the city is lukovetskiy
    https://api.openweathermap.org/data/2.5/weather?q=lukovetskiy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 255
    The name of the city is paka
    https://api.openweathermap.org/data/2.5/weather?q=paka&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 256
    The name of the city is omsukchan
    https://api.openweathermap.org/data/2.5/weather?q=omsukchan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 257
    The name of the city is bilma
    https://api.openweathermap.org/data/2.5/weather?q=bilma&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 258
    The name of the city is zachagansk
    https://api.openweathermap.org/data/2.5/weather?q=zachagansk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 259
    The name of the city is korla
    https://api.openweathermap.org/data/2.5/weather?q=korla&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 260
    The name of the city is apatou
    https://api.openweathermap.org/data/2.5/weather?q=apatou&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 261
    The name of the city is launceston
    https://api.openweathermap.org/data/2.5/weather?q=launceston&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 262
    The name of the city is kaitangata
    https://api.openweathermap.org/data/2.5/weather?q=kaitangata&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 263
    The name of the city is vero beach
    https://api.openweathermap.org/data/2.5/weather?q=vero beach&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 264
    The name of the city is tucuma
    https://api.openweathermap.org/data/2.5/weather?q=tucuma&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 265
    The name of the city is tessalit
    https://api.openweathermap.org/data/2.5/weather?q=tessalit&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 266
    The name of the city is umzimvubu
    https://api.openweathermap.org/data/2.5/weather?q=umzimvubu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 267
    The name of the city is rawatbhata
    https://api.openweathermap.org/data/2.5/weather?q=rawatbhata&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 268
    The name of the city is mys shmidta
    https://api.openweathermap.org/data/2.5/weather?q=mys shmidta&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 269
    The name of the city is olinda
    https://api.openweathermap.org/data/2.5/weather?q=olinda&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 270
    The name of the city is ayr
    https://api.openweathermap.org/data/2.5/weather?q=ayr&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 271
    The name of the city is bumba
    https://api.openweathermap.org/data/2.5/weather?q=bumba&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 272
    The name of the city is laguna
    https://api.openweathermap.org/data/2.5/weather?q=laguna&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 273
    The name of the city is coahuayana
    https://api.openweathermap.org/data/2.5/weather?q=coahuayana&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 274
    The name of the city is angoche
    https://api.openweathermap.org/data/2.5/weather?q=angoche&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 275
    The name of the city is grenaa
    https://api.openweathermap.org/data/2.5/weather?q=grenaa&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 276
    The name of the city is birao
    https://api.openweathermap.org/data/2.5/weather?q=birao&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 277
    The name of the city is kirakira
    https://api.openweathermap.org/data/2.5/weather?q=kirakira&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 278
    The name of the city is cap malheureux
    https://api.openweathermap.org/data/2.5/weather?q=cap malheureux&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 279
    The name of the city is wajid
    https://api.openweathermap.org/data/2.5/weather?q=wajid&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 280
    The name of the city is nikolskoye
    https://api.openweathermap.org/data/2.5/weather?q=nikolskoye&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 281
    The name of the city is svetlogorsk
    https://api.openweathermap.org/data/2.5/weather?q=svetlogorsk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 282
    The name of the city is kiratpur
    https://api.openweathermap.org/data/2.5/weather?q=kiratpur&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 283
    The name of the city is khasan
    https://api.openweathermap.org/data/2.5/weather?q=khasan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 284
    The name of the city is mkokotoni
    https://api.openweathermap.org/data/2.5/weather?q=mkokotoni&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 285
    The name of the city is indaial
    https://api.openweathermap.org/data/2.5/weather?q=indaial&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 286
    The name of the city is atyuryevo
    https://api.openweathermap.org/data/2.5/weather?q=atyuryevo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 287
    The name of the city is monrovia
    https://api.openweathermap.org/data/2.5/weather?q=monrovia&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 288
    The name of the city is nizhneyansk
    https://api.openweathermap.org/data/2.5/weather?q=nizhneyansk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 289
    The name of the city is sao joao da barra
    https://api.openweathermap.org/data/2.5/weather?q=sao joao da barra&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 290
    The name of the city is kargasok
    https://api.openweathermap.org/data/2.5/weather?q=kargasok&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 291
    The name of the city is bilibino
    https://api.openweathermap.org/data/2.5/weather?q=bilibino&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 292
    The name of the city is port-gentil
    https://api.openweathermap.org/data/2.5/weather?q=port-gentil&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 293
    The name of the city is tutoia
    https://api.openweathermap.org/data/2.5/weather?q=tutoia&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 294
    The name of the city is nelson bay
    https://api.openweathermap.org/data/2.5/weather?q=nelson bay&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 295
    The name of the city is sanmenxia
    https://api.openweathermap.org/data/2.5/weather?q=sanmenxia&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 296
    The name of the city is aklavik
    https://api.openweathermap.org/data/2.5/weather?q=aklavik&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 297
    The name of the city is tsihombe
    https://api.openweathermap.org/data/2.5/weather?q=tsihombe&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 298
    The name of the city is mehamn
    https://api.openweathermap.org/data/2.5/weather?q=mehamn&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 299
    The name of the city is grand gaube
    https://api.openweathermap.org/data/2.5/weather?q=grand gaube&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 300
    The name of the city is hobyo
    https://api.openweathermap.org/data/2.5/weather?q=hobyo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 301
    The name of the city is taiyuan
    https://api.openweathermap.org/data/2.5/weather?q=taiyuan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 302
    The name of the city is yumen
    https://api.openweathermap.org/data/2.5/weather?q=yumen&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 303
    The name of the city is dunedin
    https://api.openweathermap.org/data/2.5/weather?q=dunedin&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 304
    The name of the city is kipini
    https://api.openweathermap.org/data/2.5/weather?q=kipini&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 305
    The name of the city is hede
    https://api.openweathermap.org/data/2.5/weather?q=hede&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 306
    The name of the city is tupelo
    https://api.openweathermap.org/data/2.5/weather?q=tupelo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 307
    The name of the city is abu kamal
    https://api.openweathermap.org/data/2.5/weather?q=abu kamal&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 308
    The name of the city is igualada
    https://api.openweathermap.org/data/2.5/weather?q=igualada&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 309
    The name of the city is tulun
    https://api.openweathermap.org/data/2.5/weather?q=tulun&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 310
    The name of the city is ampanihy
    https://api.openweathermap.org/data/2.5/weather?q=ampanihy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 311
    The name of the city is luanda
    https://api.openweathermap.org/data/2.5/weather?q=luanda&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 312
    The name of the city is ranong
    https://api.openweathermap.org/data/2.5/weather?q=ranong&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 313
    The name of the city is kimberley
    https://api.openweathermap.org/data/2.5/weather?q=kimberley&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 314
    The name of the city is yuanping
    https://api.openweathermap.org/data/2.5/weather?q=yuanping&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 315
    The name of the city is doctor pedro p. pena
    https://api.openweathermap.org/data/2.5/weather?q=doctor pedro p. pena&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 316
    The name of the city is shubarshi
    https://api.openweathermap.org/data/2.5/weather?q=shubarshi&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 317
    The name of the city is hamilton
    https://api.openweathermap.org/data/2.5/weather?q=hamilton&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 318
    The name of the city is terrace
    https://api.openweathermap.org/data/2.5/weather?q=terrace&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 319
    The name of the city is longyearbyen
    https://api.openweathermap.org/data/2.5/weather?q=longyearbyen&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 320
    The name of the city is bolshoy istok
    https://api.openweathermap.org/data/2.5/weather?q=bolshoy istok&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 321
    The name of the city is talakan
    https://api.openweathermap.org/data/2.5/weather?q=talakan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 322
    The name of the city is straumen
    https://api.openweathermap.org/data/2.5/weather?q=straumen&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 323
    The name of the city is bonavista
    https://api.openweathermap.org/data/2.5/weather?q=bonavista&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 324
    The name of the city is provideniya
    https://api.openweathermap.org/data/2.5/weather?q=provideniya&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 325
    The name of the city is sakmara
    https://api.openweathermap.org/data/2.5/weather?q=sakmara&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 326
    The name of the city is salumbar
    https://api.openweathermap.org/data/2.5/weather?q=salumbar&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 327
    The name of the city is richards bay
    https://api.openweathermap.org/data/2.5/weather?q=richards bay&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 328
    The name of the city is sentyabrskiy
    https://api.openweathermap.org/data/2.5/weather?q=sentyabrskiy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 329
    The name of the city is katsuura
    https://api.openweathermap.org/data/2.5/weather?q=katsuura&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 330
    The name of the city is faya
    https://api.openweathermap.org/data/2.5/weather?q=faya&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 331
    The name of the city is porto walter
    https://api.openweathermap.org/data/2.5/weather?q=porto walter&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 332
    The name of the city is woodward
    https://api.openweathermap.org/data/2.5/weather?q=woodward&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 333
    The name of the city is hasaki
    https://api.openweathermap.org/data/2.5/weather?q=hasaki&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 334
    The name of the city is san quintin
    https://api.openweathermap.org/data/2.5/weather?q=san quintin&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 335
    The name of the city is pangai
    https://api.openweathermap.org/data/2.5/weather?q=pangai&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 336
    The name of the city is narsaq
    https://api.openweathermap.org/data/2.5/weather?q=narsaq&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 337
    The name of the city is walvis bay
    https://api.openweathermap.org/data/2.5/weather?q=walvis bay&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 338
    The name of the city is dakar
    https://api.openweathermap.org/data/2.5/weather?q=dakar&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 339
    The name of the city is lerwick
    https://api.openweathermap.org/data/2.5/weather?q=lerwick&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 340
    The name of the city is gujar khan
    https://api.openweathermap.org/data/2.5/weather?q=gujar khan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 341
    The name of the city is koumac
    https://api.openweathermap.org/data/2.5/weather?q=koumac&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 342
    The name of the city is half moon bay
    https://api.openweathermap.org/data/2.5/weather?q=half moon bay&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 343
    The name of the city is camacha
    https://api.openweathermap.org/data/2.5/weather?q=camacha&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 344
    The name of the city is nordkisa
    https://api.openweathermap.org/data/2.5/weather?q=nordkisa&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 345
    The name of the city is ust-nera
    https://api.openweathermap.org/data/2.5/weather?q=ust-nera&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 346
    The name of the city is beeville
    https://api.openweathermap.org/data/2.5/weather?q=beeville&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 347
    The name of the city is mana
    https://api.openweathermap.org/data/2.5/weather?q=mana&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 348
    The name of the city is tevriz
    https://api.openweathermap.org/data/2.5/weather?q=tevriz&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 349
    The name of the city is hagere hiywet
    https://api.openweathermap.org/data/2.5/weather?q=hagere hiywet&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 350
    The name of the city is kita
    https://api.openweathermap.org/data/2.5/weather?q=kita&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 351
    The name of the city is tuggurt
    https://api.openweathermap.org/data/2.5/weather?q=tuggurt&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 352
    The name of the city is saint-joseph
    https://api.openweathermap.org/data/2.5/weather?q=saint-joseph&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 353
    The name of the city is hambantota
    https://api.openweathermap.org/data/2.5/weather?q=hambantota&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 354
    The name of the city is fuling
    https://api.openweathermap.org/data/2.5/weather?q=fuling&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 355
    The name of the city is del rio
    https://api.openweathermap.org/data/2.5/weather?q=del rio&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 356
    The name of the city is mutoko
    https://api.openweathermap.org/data/2.5/weather?q=mutoko&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 357
    The name of the city is lagoa
    https://api.openweathermap.org/data/2.5/weather?q=lagoa&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 358
    The name of the city is puerto madero
    https://api.openweathermap.org/data/2.5/weather?q=puerto madero&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 359
    The name of the city is fano
    https://api.openweathermap.org/data/2.5/weather?q=fano&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 360
    The name of the city is vestmannaeyjar
    https://api.openweathermap.org/data/2.5/weather?q=vestmannaeyjar&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 361
    The name of the city is port hawkesbury
    https://api.openweathermap.org/data/2.5/weather?q=port hawkesbury&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 362
    The name of the city is canchungo
    https://api.openweathermap.org/data/2.5/weather?q=canchungo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 363
    The name of the city is makakilo city
    https://api.openweathermap.org/data/2.5/weather?q=makakilo city&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 364
    The name of the city is phan rang
    https://api.openweathermap.org/data/2.5/weather?q=phan rang&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 365
    The name of the city is kiama
    https://api.openweathermap.org/data/2.5/weather?q=kiama&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 366
    The name of the city is santa cruz del norte
    https://api.openweathermap.org/data/2.5/weather?q=santa cruz del norte&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 367
    The name of the city is itaituba
    https://api.openweathermap.org/data/2.5/weather?q=itaituba&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 368
    The name of the city is havre-saint-pierre
    https://api.openweathermap.org/data/2.5/weather?q=havre-saint-pierre&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 369
    The name of the city is meyungs
    https://api.openweathermap.org/data/2.5/weather?q=meyungs&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 370
    The name of the city is la primavera
    https://api.openweathermap.org/data/2.5/weather?q=la primavera&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 371
    The name of the city is prieska
    https://api.openweathermap.org/data/2.5/weather?q=prieska&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 372
    The name of the city is springfield
    https://api.openweathermap.org/data/2.5/weather?q=springfield&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 373
    The name of the city is vila velha
    https://api.openweathermap.org/data/2.5/weather?q=vila velha&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 374
    The name of the city is solnechnyy
    https://api.openweathermap.org/data/2.5/weather?q=solnechnyy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 375
    The name of the city is mbandaka
    https://api.openweathermap.org/data/2.5/weather?q=mbandaka&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 376
    The name of the city is usinsk
    https://api.openweathermap.org/data/2.5/weather?q=usinsk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 377
    The name of the city is bahile
    https://api.openweathermap.org/data/2.5/weather?q=bahile&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 378
    The name of the city is copiapo
    https://api.openweathermap.org/data/2.5/weather?q=copiapo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 379
    The name of the city is payakumbuh
    https://api.openweathermap.org/data/2.5/weather?q=payakumbuh&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 380
    The name of the city is sredneuralsk
    https://api.openweathermap.org/data/2.5/weather?q=sredneuralsk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 381
    The name of the city is sayyan
    https://api.openweathermap.org/data/2.5/weather?q=sayyan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 382
    The name of the city is teguise
    https://api.openweathermap.org/data/2.5/weather?q=teguise&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 383
    The name of the city is mahaicony
    https://api.openweathermap.org/data/2.5/weather?q=mahaicony&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 384
    The name of the city is nabire
    https://api.openweathermap.org/data/2.5/weather?q=nabire&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 385
    The name of the city is vaitupu
    https://api.openweathermap.org/data/2.5/weather?q=vaitupu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 386
    The name of the city is snezhnogorsk
    https://api.openweathermap.org/data/2.5/weather?q=snezhnogorsk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 387
    The name of the city is abha
    https://api.openweathermap.org/data/2.5/weather?q=abha&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 388
    The name of the city is vernon
    https://api.openweathermap.org/data/2.5/weather?q=vernon&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 389
    The name of the city is pisco
    https://api.openweathermap.org/data/2.5/weather?q=pisco&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 390
    The name of the city is tawkar
    https://api.openweathermap.org/data/2.5/weather?q=tawkar&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 391
    The name of the city is karratha
    https://api.openweathermap.org/data/2.5/weather?q=karratha&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 392
    The name of the city is tuy hoa
    https://api.openweathermap.org/data/2.5/weather?q=tuy hoa&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 393
    The name of the city is carnduff
    https://api.openweathermap.org/data/2.5/weather?q=carnduff&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 394
    The name of the city is alta floresta
    https://api.openweathermap.org/data/2.5/weather?q=alta floresta&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 395
    The name of the city is kerepehi
    https://api.openweathermap.org/data/2.5/weather?q=kerepehi&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 396
    The name of the city is ancud
    https://api.openweathermap.org/data/2.5/weather?q=ancud&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 397
    The name of the city is halalo
    https://api.openweathermap.org/data/2.5/weather?q=halalo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 398
    The name of the city is san cristobal
    https://api.openweathermap.org/data/2.5/weather?q=san cristobal&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 399
    The name of the city is toamasina
    https://api.openweathermap.org/data/2.5/weather?q=toamasina&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 400
    The name of the city is antofagasta
    https://api.openweathermap.org/data/2.5/weather?q=antofagasta&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 401
    The name of the city is tungkang
    https://api.openweathermap.org/data/2.5/weather?q=tungkang&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 402
    The name of the city is zhuhai
    https://api.openweathermap.org/data/2.5/weather?q=zhuhai&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 403
    The name of the city is soubre
    https://api.openweathermap.org/data/2.5/weather?q=soubre&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 404
    The name of the city is ucluelet
    https://api.openweathermap.org/data/2.5/weather?q=ucluelet&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 405
    The name of the city is ribeira grande
    https://api.openweathermap.org/data/2.5/weather?q=ribeira grande&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 406
    The name of the city is aygut
    https://api.openweathermap.org/data/2.5/weather?q=aygut&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 407
    The name of the city is portland
    https://api.openweathermap.org/data/2.5/weather?q=portland&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 408
    The name of the city is kendari
    https://api.openweathermap.org/data/2.5/weather?q=kendari&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 409
    The name of the city is qui nhon
    https://api.openweathermap.org/data/2.5/weather?q=qui nhon&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 410
    The name of the city is saluzzo
    https://api.openweathermap.org/data/2.5/weather?q=saluzzo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 411
    The name of the city is urengoy
    https://api.openweathermap.org/data/2.5/weather?q=urengoy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 412
    The name of the city is country club
    https://api.openweathermap.org/data/2.5/weather?q=country club&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 413
    The name of the city is bireun
    https://api.openweathermap.org/data/2.5/weather?q=bireun&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 414
    The name of the city is ambilobe
    https://api.openweathermap.org/data/2.5/weather?q=ambilobe&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 415
    The name of the city is peniche
    https://api.openweathermap.org/data/2.5/weather?q=peniche&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 416
    The name of the city is stornoway
    https://api.openweathermap.org/data/2.5/weather?q=stornoway&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 417
    The name of the city is solovetskiy
    https://api.openweathermap.org/data/2.5/weather?q=solovetskiy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 418
    The name of the city is maputo
    https://api.openweathermap.org/data/2.5/weather?q=maputo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 419
    The name of the city is luba
    https://api.openweathermap.org/data/2.5/weather?q=luba&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 420
    The name of the city is matay
    https://api.openweathermap.org/data/2.5/weather?q=matay&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 421
    The name of the city is otjimbingwe
    https://api.openweathermap.org/data/2.5/weather?q=otjimbingwe&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 422
    The name of the city is acala
    https://api.openweathermap.org/data/2.5/weather?q=acala&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 423
    The name of the city is auki
    https://api.openweathermap.org/data/2.5/weather?q=auki&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 424
    The name of the city is afmadu
    https://api.openweathermap.org/data/2.5/weather?q=afmadu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 425
    The name of the city is methoni
    https://api.openweathermap.org/data/2.5/weather?q=methoni&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 426
    The name of the city is gunjur
    https://api.openweathermap.org/data/2.5/weather?q=gunjur&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 427
    The name of the city is khonuu
    https://api.openweathermap.org/data/2.5/weather?q=khonuu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 428
    The name of the city is cockburn town
    https://api.openweathermap.org/data/2.5/weather?q=cockburn town&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 429
    The name of the city is tooele
    https://api.openweathermap.org/data/2.5/weather?q=tooele&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 430
    The name of the city is uruacu
    https://api.openweathermap.org/data/2.5/weather?q=uruacu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 431
    The name of the city is burnie
    https://api.openweathermap.org/data/2.5/weather?q=burnie&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 432
    The name of the city is millinocket
    https://api.openweathermap.org/data/2.5/weather?q=millinocket&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 433
    The name of the city is azad shahr
    https://api.openweathermap.org/data/2.5/weather?q=azad shahr&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 434
    The name of the city is hailar
    https://api.openweathermap.org/data/2.5/weather?q=hailar&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 435
    The name of the city is irituia
    https://api.openweathermap.org/data/2.5/weather?q=irituia&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 436
    The name of the city is tupik
    https://api.openweathermap.org/data/2.5/weather?q=tupik&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 437
    The name of the city is paciran
    https://api.openweathermap.org/data/2.5/weather?q=paciran&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 438
    The name of the city is lichinga
    https://api.openweathermap.org/data/2.5/weather?q=lichinga&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 439
    The name of the city is iranshahr
    https://api.openweathermap.org/data/2.5/weather?q=iranshahr&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 440
    The name of the city is pemangkat
    https://api.openweathermap.org/data/2.5/weather?q=pemangkat&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 441
    The name of the city is zhigansk
    https://api.openweathermap.org/data/2.5/weather?q=zhigansk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 442
    The name of the city is bardiyah
    https://api.openweathermap.org/data/2.5/weather?q=bardiyah&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 443
    The name of the city is kudahuvadhoo
    https://api.openweathermap.org/data/2.5/weather?q=kudahuvadhoo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 444
    The name of the city is taonan
    https://api.openweathermap.org/data/2.5/weather?q=taonan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 445
    The name of the city is vuktyl
    https://api.openweathermap.org/data/2.5/weather?q=vuktyl&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 446
    The name of the city is fuerte olimpo
    https://api.openweathermap.org/data/2.5/weather?q=fuerte olimpo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 447
    The name of the city is esperance
    https://api.openweathermap.org/data/2.5/weather?q=esperance&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 448
    The name of the city is vilyuysk
    https://api.openweathermap.org/data/2.5/weather?q=vilyuysk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 449
    The name of the city is xichang
    https://api.openweathermap.org/data/2.5/weather?q=xichang&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 450
    The name of the city is burdur
    https://api.openweathermap.org/data/2.5/weather?q=burdur&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 451
    The name of the city is komsomolskiy
    https://api.openweathermap.org/data/2.5/weather?q=komsomolskiy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 452
    The name of the city is gimli
    https://api.openweathermap.org/data/2.5/weather?q=gimli&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 453
    The name of the city is chifeng
    https://api.openweathermap.org/data/2.5/weather?q=chifeng&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 454
    The name of the city is kaniama
    https://api.openweathermap.org/data/2.5/weather?q=kaniama&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 455
    The name of the city is hillandale
    https://api.openweathermap.org/data/2.5/weather?q=hillandale&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 456
    The name of the city is margate
    https://api.openweathermap.org/data/2.5/weather?q=margate&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 457
    The name of the city is ylivieska
    https://api.openweathermap.org/data/2.5/weather?q=ylivieska&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 458
    The name of the city is paso de carrasco
    https://api.openweathermap.org/data/2.5/weather?q=paso de carrasco&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 459
    The name of the city is sumenep
    https://api.openweathermap.org/data/2.5/weather?q=sumenep&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 460
    The name of the city is alindao
    https://api.openweathermap.org/data/2.5/weather?q=alindao&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 461
    The name of the city is yulara
    https://api.openweathermap.org/data/2.5/weather?q=yulara&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 462
    The name of the city is lagos
    https://api.openweathermap.org/data/2.5/weather?q=lagos&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 463
    The name of the city is great yarmouth
    https://api.openweathermap.org/data/2.5/weather?q=great yarmouth&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 464
    The name of the city is te anau
    https://api.openweathermap.org/data/2.5/weather?q=te anau&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 465
    The name of the city is calama
    https://api.openweathermap.org/data/2.5/weather?q=calama&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 466
    The name of the city is sao miguel do araguaia
    https://api.openweathermap.org/data/2.5/weather?q=sao miguel do araguaia&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 467
    The name of the city is san fernando
    https://api.openweathermap.org/data/2.5/weather?q=san fernando&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 468
    The name of the city is homer
    https://api.openweathermap.org/data/2.5/weather?q=homer&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 469
    The name of the city is northam
    https://api.openweathermap.org/data/2.5/weather?q=northam&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 470
    The name of the city is agirish
    https://api.openweathermap.org/data/2.5/weather?q=agirish&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 471
    The name of the city is arrecife
    https://api.openweathermap.org/data/2.5/weather?q=arrecife&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 472
    The name of the city is sant feliu de guixols
    https://api.openweathermap.org/data/2.5/weather?q=sant feliu de guixols&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 473
    The name of the city is guerrero negro
    https://api.openweathermap.org/data/2.5/weather?q=guerrero negro&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 474
    The name of the city is tabiauea
    https://api.openweathermap.org/data/2.5/weather?q=tabiauea&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 475
    The name of the city is topeka
    https://api.openweathermap.org/data/2.5/weather?q=topeka&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 476
    The name of the city is zambezi
    https://api.openweathermap.org/data/2.5/weather?q=zambezi&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 477
    The name of the city is mumford
    https://api.openweathermap.org/data/2.5/weather?q=mumford&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 478
    The name of the city is biak
    https://api.openweathermap.org/data/2.5/weather?q=biak&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 479
    The name of the city is cochrane
    https://api.openweathermap.org/data/2.5/weather?q=cochrane&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 480
    The name of the city is lake cowichan
    https://api.openweathermap.org/data/2.5/weather?q=lake cowichan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 481
    The name of the city is saleaula
    https://api.openweathermap.org/data/2.5/weather?q=saleaula&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 482
    The name of the city is vermillion
    https://api.openweathermap.org/data/2.5/weather?q=vermillion&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 483
    The name of the city is krasnaya gorka
    https://api.openweathermap.org/data/2.5/weather?q=krasnaya gorka&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 484
    The name of the city is dalnerechensk
    https://api.openweathermap.org/data/2.5/weather?q=dalnerechensk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 485
    The name of the city is samusu
    https://api.openweathermap.org/data/2.5/weather?q=samusu&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 486
    The name of the city is porto novo
    https://api.openweathermap.org/data/2.5/weather?q=porto novo&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 487
    The name of the city is westport
    https://api.openweathermap.org/data/2.5/weather?q=westport&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 488
    The name of the city is abbeville
    https://api.openweathermap.org/data/2.5/weather?q=abbeville&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 489
    The name of the city is winsum
    https://api.openweathermap.org/data/2.5/weather?q=winsum&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 490
    The name of the city is hays
    https://api.openweathermap.org/data/2.5/weather?q=hays&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 491
    The name of the city is begunitsy
    https://api.openweathermap.org/data/2.5/weather?q=begunitsy&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 492
    The name of the city is tongchuan
    https://api.openweathermap.org/data/2.5/weather?q=tongchuan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 493
    The name of the city is kerki
    https://api.openweathermap.org/data/2.5/weather?q=kerki&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 494
    The name of the city is deori khas
    https://api.openweathermap.org/data/2.5/weather?q=deori khas&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 495
    The name of the city is high level
    https://api.openweathermap.org/data/2.5/weather?q=high level&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 496
    The name of the city is matam
    https://api.openweathermap.org/data/2.5/weather?q=matam&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 497
    The name of the city is poso
    https://api.openweathermap.org/data/2.5/weather?q=poso&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 498
    The name of the city is cabo san lucas
    https://api.openweathermap.org/data/2.5/weather?q=cabo san lucas&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 499
    The name of the city is najran
    https://api.openweathermap.org/data/2.5/weather?q=najran&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 500
    The name of the city is ashington
    https://api.openweathermap.org/data/2.5/weather?q=ashington&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 501
    The name of the city is los llanos de aridane
    https://api.openweathermap.org/data/2.5/weather?q=los llanos de aridane&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 502
    The name of the city is ketchikan
    https://api.openweathermap.org/data/2.5/weather?q=ketchikan&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 503
    The name of the city is san patricio
    https://api.openweathermap.org/data/2.5/weather?q=san patricio&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 504
    The name of the city is inza
    https://api.openweathermap.org/data/2.5/weather?q=inza&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 505
    The name of the city is jibuti
    https://api.openweathermap.org/data/2.5/weather?q=jibuti&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 506
    The name of the city is hami
    https://api.openweathermap.org/data/2.5/weather?q=hami&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 507
    The name of the city is kursk
    https://api.openweathermap.org/data/2.5/weather?q=kursk&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    we are now on city number 508
    The name of the city is oyotun
    https://api.openweathermap.org/data/2.5/weather?q=oyotun&appid=2316b03031cfb536c195835482e8bc0c&units=imperial
    __________________________________________________________________________________________
    


```python
new_data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lng</th>
      <th>City</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>87.097237</td>
      <td>52.248082</td>
      <td>belushya guba</td>
      <td>38.33</td>
      <td>100</td>
      <td>24</td>
      <td>18.28</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-70.662926</td>
      <td>-11.955065</td>
      <td>cape town</td>
      <td>59.00</td>
      <td>67</td>
      <td>0</td>
      <td>18.34</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-83.341232</td>
      <td>113.235407</td>
      <td>albany</td>
      <td>70.21</td>
      <td>73</td>
      <td>75</td>
      <td>6.93</td>
    </tr>
    <tr>
      <th>3</th>
      <td>58.523751</td>
      <td>-96.934863</td>
      <td>thompson</td>
      <td>53.60</td>
      <td>58</td>
      <td>40</td>
      <td>5.82</td>
    </tr>
    <tr>
      <th>4</th>
      <td>72.239971</td>
      <td>-71.609458</td>
      <td>clyde river</td>
      <td>30.20</td>
      <td>86</td>
      <td>90</td>
      <td>18.34</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Latitude Vs Temperature Scatter Plot
plt.scatter(new_data["Lat"],new_data["Temperature"],marker ="o")
plt.title("Latitude Vs. Temperature")
plt.xlabel("Latitude")
plt.ylabel("Temperature (F)")
sns.set()
plt.show()
```


![png](output_7_0.png)



```python
#Humidity Vs. Latitude Scatter Plot
plt.scatter(new_data["Lat"],new_data["Humidity"],marker ="o")
plt.title("Latitude Vs. Humidity")
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")
sns.set()
plt.show()
```


![png](output_8_0.png)



```python
#Cloudiness Vs. Latitude Scatter Plot
plt.scatter(new_data["Lat"],new_data["Clouds"],marker ="o")
plt.title("Latitude Vs. Cloudiness")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
sns.set()
plt.show()
```


![png](output_9_0.png)



```python
#Wind Speed Vs. Latitude Scatter Plot
plt.scatter(new_data["Lat"],new_data["Wind Speed"],marker ="o")
plt.title("Latitude Vs. Wind Speed")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (mph)")
sns.set()
plt.show()
```


![png](output_10_0.png)



```python

```
