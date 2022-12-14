## Mapas en Python : Primeros Pasos.

A traves de este ejemplo sencillo vamos a aprender un modo de generar mapas en Python de manera simple. 

En primer lugar importamos las librerias necesarias. 

```python
import pandas as pd
import numpy as np
import folium
import geopandas as gpd
import matplotlib.pyplot as plt
%matplotlib inline

```

Si la importacion de Geopandas presenta algún problema con las librerias de GDAL o Fiona es necesario importarlas previamente a mano de la siguiente forma: 

FIONA

- Ingresar en: https://www.lfd.uci.edu/~gohlke/pythonlibs/#fiona
- Descargar el archivo acorde a tu version de Python y a tu procesador (Por ejemplo, para Python 3.10 y procesador de 64 bits vas a necesitar el archivo Fiona‑1.8.21‑cp310‑cp310‑win_amd64.whl) 
- Ejecutar en la consola el siguiente comando :  pip install path/to/Fiona-XXXX.whl

GDAL

- Ingresar en: https://www.lfd.uci.edu/~gohlke/pythonlibs/#gdal
- Descargar el archivo acorde a tu version de Python y a tu procesador (Por ejemplo, para Python 3.10 y procesador de 64 bits vas a necesitar el archivo GDAL‑3.4.3‑cp310‑cp310‑win_amd64.whl) 
- Ejecutar en la consola el siguiente comando :  pip install path/to/GDAL-XXXX.whl



```python
df_places = gpd.read_file('geojson/ign_provincia.json')
df_places2=df_places[df_places['IN1']=='06']
df_places2
df_places.plot()
```
![Argentina](src/argentina.png)

```python
df_places = gpd.read_file('geojson/departamento_3.json')
df_places2=df_places[(df_places['in1'].str.slice(0, 3)=='006') | (df_places['in1'].str.slice(0, 3)=='002')]
df_places2.plot()

```
![BsAs](src/buenos_aires.png)

```python
f, ax = plt.subplots(1, figsize=(10, 10))
ax = df_places2.plot(ax=ax)
ax.set_xlim([-59, -58])
ax.set_ylim([-35, -34.2])
plt.show()
```
![CabayGba](src/caba_y_gba.png)

```python
m = folium.Map(location=[-34.6, -58.4], zoom_start=11, tiles='CartoDB positron')
m.save("folium/mapa_base.html") 
```
https://pedrocarrancio.github.io/PYTHON_mapa_hospitales_amba/folium/mapa_base.html

![MapaBase](src/mapa_base.png)

```python
for _, r in df_places2.iterrows():
    # Without simplifying the representation of each borough,
    # the map might not be displayed
    sim_geo = gpd.GeoSeries(r['geometry']).simplify(tolerance=0.001)
    geo_j = sim_geo.to_json()
    if (r['in1'][0:3]=='002'):
        geo_j = folium.GeoJson(data=geo_j,
                           style_function=lambda x: {'fillColor': 'orange','fillOpacity':0.3})
    else :            
        geo_j = folium.GeoJson(data=geo_j,
                           style_function=lambda x: {'fillColor': 'green','fillOpacity':0.3})            
    folium.Popup(r['fna']).add_to(geo_j)
    geo_j.add_to(m)
m.save("folium/mapa_departamental.html") 
```
https://pedrocarrancio.github.io/PYTHON_mapa_hospitales_amba/folium/mapa_departamental.html

![MapaDepartamental](src/mapa_departamental.png)
```python
hsp = pd.read_csv('hospitales_coordenadas.csv',dtype=str)

```

```python
for _, r in hsp.iterrows():
    lat = r['latitude']
    lon = r['longitude']
    folium.Marker(location=[lat, lon],
                  popup='Nombre: {} <br> Direccion: {}'.format(r['name'], r['label'])).add_to(m)

m.save("folium/mapa_departamental_con_marcadores.html") 
```
https://pedrocarrancio.github.io/PYTHON_mapa_hospitales_amba/folium/mapa_departamental_con_marcadores.html

![MapaDepartamentalConMarcadores](src/mapa_departamental_con_marcadores.png)