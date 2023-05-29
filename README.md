# maya-byte.github.io
DB_HOST = "blindspot.media.mit.edu"
DB_NAME = "homehub"
DB_USER = "maya"
DB_PASS = "wateriscool"
DB_PORT= "5444"
import psycopg2
import matplotlib.pyplot as plt
from datetime import datetime
import pandas as pd
import random
import numpy as np

## Humidity Sensors Comparison - With Local Weather

## CHANGE COLORS 
conn = psycopg2.connect(dbname=DB_NAME, user=DB_USER, password=DB_PASS, host=DB_HOST, port=DB_PORT)

cur = conn.cursor()

query = "select mac_add, use from th_sensors where paired_with = 'C8:F0:9E:06:72:D4';"

cur.execute(query)

temp_sensors = cur.fetchall()

temp_history = []
for temp in temp_sensors:
  query = "select datetime, humidity from th_data where mac_add='" + temp[0] + "' and DATE '2023-03-22' < datetime and datetime <  DATE '2023-05-21';"
  cur.execute(query)
  something = cur.fetchall()
  temp_history.append((temp, something))

plt.style.use('_mpl-gallery')

for temp_data in temp_history:
  x = []
  y = []
  for data in temp_data[1]:
    x.append(data[0])
    y.append(data[1])
  plt.scatter(x, y, label=temp_data[0][1], zorder = 2)

query = "select datetime, humidity from homehub_climatedatadb where mac_add='C8:F0:9E:06:72:D4' and DATE '2023-03-22' < datetime and datetime <  DATE '2023-05-21';"
cur.execute(query)
something = cur.fetchall()
local = ((('C8:F0:9E:06:72:D4', 'Local Weather'), something))

x = []
y = []
for data in local[1]:
    x.append(data[0])
    y.append(data[1])
col = (np.random.random(), np.random.random(), np.random.random())
plt.plot(x, y, label=local[0][1], alpha = 0.4, zorder = 1)



fig = plt.gcf()
plt.xlabel("Date Range")
plt.ylabel("Humidity")
fig.set_size_inches(15, 8)
plt.legend(loc="upper right", fontsize = '20')
plt.xticks(fontsize=15)
plt.show()
