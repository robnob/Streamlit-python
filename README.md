# Streamlit-python
## Settings

1. Open a terminal and then type down "atom .", which is going to opne the Atom IDE
2. **First Code (in Atom):**
- import streamlit as st 
- st.title("Hola Mundo") 
- save it as app.py
3. **In the terminal:**
- streamlit run app.py
- it would open a navigator with the web page title
4. Add an exlamation point to "Hola Mundo!" and save the file, in the browser selects "Always rerun" so that any change saved in the file is shown on it (live coding). In this way it is going to use the deltas and not to re-charge the whole page again.
5. **Adding markdowns:**
- st.markdown("## My first streamlit dashboard!)

## First Version
import streamlit as st
import pandas as pd
import numpy as np

DATA_URL=(
"/home/Desktop/Project/Motor_Vehicle_Collisions_-_Crashes.csv"
)

st.title("Motor Vehicle Collisions in New York City")

st.markdown("This application is a Streamlit dashboard that can be used"

"to analyze motor vehicle collisions in NYC")

An emoji of NYC has been added in the markdown, because it is capable of reading emojis.

## Creating a function
@st.cache(persist = True) This is so that the data persists in cache and only when the original data has changed the file would be re-read
df load_data(nrows):

  data = pd.read_csv(DATA_URL, nrows = nrows, parse_dates=[['CRASH_DATE','CRASH_TIME']])
  
  data.dropna(subset=['LATITUDE', 'LONGITUDE'], inplace = True)
  
  lowercase = lambda x: str(x).lower()
  
  data.rename(lowercase, axis='columns', inplace = True)
  
  data.rename(columns={'crash_date_crash_time', 'date/time', inplace=True})
  
  return data
  
  data = load_data(100000)
  
  original_data = data
  
  ## Showing the data in a table
  if st.checkbox("Show Raw Data", False):
  
  -st.subheader('Raw Data')
  
  -st.write(data)
  
  ## Visualize data on a map
  st.header("Where are the most people injured in NYC?")
  
  injured_people=st.slider("Number of people injured in vehicle collissions", 0,19)
  
  st.map(data.query("injured_persons >= @injured_people")["latitude", "longitude"]].dropna(how="any"))
 
## Filtering Data and Interactive Maps
st.header("How many collisions occurred during a given time of day ?")

hour = st.selectbox("Hour to look at", range(0,24),1)   *Differences of 1 hour

or

hour = st.sidebar.slider("Hour to look at", 0 , 23)

data = data[data['date/time'].dt.hour == hour]   *In this case is really filtering the Raw Data

## Plot filtered data on a 3D Interactive Map
st.markdown("Vehicle collision between %i:00 and %i:00" % (hour, (hour+1) % 24)

midpoint = (np.average(data["latitude"]), np.average(data["longitude"]))

import pydeck as pdk

st.write(pdk.Deck(

  map_style ="mapbox://styles/mapbox/light-v9",
  
  initial_view_state={
  
    "latitude": midpoint[0],
    
    "longitude":midpoint[1],
    
    "zoom": 11,
    
    "pitch":50,
  
  },
  layers=[
  
    pdk.Layer(
    
    "HexagonLayer",
    
    data = data[['date/time','latitude', 'longitude']],
    
    get_position=['longitude', 'latitude'],
    
    radius = 100,
    
    extruded = True,
    
    pickable = True,
    
    elevation_scale = 4,
    
    elevation_range[0,1000],
       
    ),
    
  ],
  
))

## Charts and histograms
import plotly.express as px

st.subheader("Breakdown by minute between %i:00 and %i:00" % (hour, (hour +1)%24))

filtered = data[

  (data['date/time'].dt.hour >= hour) & (data['date/time'].dt.hour < (hour+1))

]

hist = np.histogram(filtered['date/time'].dt.minute, bins = 60, range=(0,60))[0]

chart_data = pd.DataFrame({'minute': range(60),'crashes' : hist })

fig = px.bar(chart_data, x='minute', y='crashes', hover_data=['minute', 'crashes'], height=400)

st.write(fig)

## Dropdowns
st.header("Top 5 dangerous streets by affected type")

select = st.selectbox('Affected type of people', ['Pedestrians', 'Cyclists', 'Motorists'])

if select == 'Pedestrians':
  
  st.write(original_data.query("injured_pedestrians >=1")[["on_street_name", "injured_pedestrians"]].sort_values(by=["injured_pedestrians"], ascending = False).dropna(how='any')[:5])
  
elif select == 'Cyclists':
  
  st.write(original_data.query("injured_cyclists >=1")[["on_street_name", "injured_cyclists"]].sort_values(by=["injured_cyclists"], ascending = False).dropna(how='any')[:5])
  
else:
  
  st.write(original_data.query("injured_motorists >=1")[["on_street_name", "injured_motorists"]].sort_values(by=["injured_motorists = data"], ascending = False).dropna(how='any')[:5])
  
 


