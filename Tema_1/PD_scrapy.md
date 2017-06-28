
# Web scraping con Python

El Web Scraping (o Scraping) son un conjunto de técnicas que se utilizan para obtener de forma automática el contenido que hay en páginas web a través de su código HTML.

## Búsqueda de tablas

Pandas recorre un documento HTML en busca de elementos de tipo `<table>`. 

En el siguiente ejemplo, mostramos cómo extraer la información de la página web con URL "https://es.wikipedia.org/wiki/Anexo:Municipios_de_la_Comunidad_de_Madrid". Esta página muestra datos asociados a distintas localidades de la Comunidad de Madrid. Para poder acceder al código HTML de una página web es necesario realizar una petición usando el protocolo HTTP Request/Response.



```

>>> import requests
import pandas as pd

url = "https://es.wikipedia.org/wiki/Anexo:Municipios_de_la_Comunidad_de_Madrid"
respuesta = requests.get(url)


```

Si la petición ha ido bien, podemos acceder al código HTML que contiene (puede no contener) tablas para importar.


```

>>> codigoHTML = respuesta.text
lista_dataframes = pd.read_html(codigoHTML, header=0)

```


```

>>> len(lista_dataframes)

```




    1




```

>>> lista_dataframes[0].head()

```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Nombre</th>
      <th>Población (2016)</th>
      <th>Superficie (km²)[1]</th>
      <th>Mapa</th>
      <th>Escudo</th>
      <th>Capitalidad[1]</th>
      <th>Altitud (msnm)[a] [2]</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>La Acebeda</td>
      <td>66</td>
      <td>2206</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>La Acebeda</td>
      <td>1271</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Ajalvir</td>
      <td>4440</td>
      <td>1962</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Ajalvir</td>
      <td>680</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Alameda del Valle</td>
      <td>208</td>
      <td>2501</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Alameda del Valle</td>
      <td>1104</td>
    </tr>
    <tr>
      <th>3</th>
      <td>El Álamo</td>
      <td>9017</td>
      <td>2225</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>El Álamo</td>
      <td>608</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Alcalá de Henares</td>
      <td>195 907</td>
      <td>8772</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Alcalá de Henares</td>
      <td>587</td>
    </tr>
  </tbody>
</table>
</div>



Como se puede observar, la función `pd.read_html` ignora todos los elementos contenidos en el documento HTML que no tengan nada que ver con tablas HTML.

## Otro tipo de búsquedas

Si los datos que buscamos no están en una tabla HTML, podemos usar alguna de las librerías de Python que permiten parsear el texto HTML de forma cómoda.

* BeautifullSoup 
* Scrapy
* Selenium

__BeautifulSoup__ nos aporta los métodos necesarios para obtener el contenido que hay entre las etiquetas HTML.


```

>>> from bs4 import BeautifulSoup
import requests

url = 'http://busqueda.libros.fnac.es/n710/Libros-mas-vendidos'
codigoHTML = requests.get(url).text

# Pasamos el contenido HTML de la web a un objeto BeautifulSoup()
soup = BeautifulSoup(codigoHTML, 'html.parser')

```


```

>>> # recuperamos la información de los títulos
titulos = [e.attrs['alt'] for e in soup.find_all('img',{'class':'Article-itemVisual--type-0' })]

```


```

>>> titulos[:4]

```




    ['Patria',
     'Norman y Mix',
     'Más allá del invierno',
     'Dragon Ball Serie roja nº 215/216']




```

>>> # recuperamos la información de las imágenes
imagenes = [e.attrs['data-lazyimage'] for e in soup.find_all('img',{'class':'Article-itemVisual--type-0' })]

```


```

>>> imagenes[:4]

```




    ['http://static.fnac-static.com/multimedia/Images/ES/NR/4f/60/13/1269839/1535-1.jpg',
     'http://static.fnac-static.com/multimedia/Images/ES/NR/9d/9a/14/1350301/1539-1.jpg',
     'http://static.fnac-static.com/multimedia/Images/ES/NR/17/8a/14/1346071/1539-1.jpg',
     'http://static.fnac-static.com/multimedia/Images/ES/NR/bb/c5/14/1361339/1539-1.jpg']




```

>>> tabla = pd.DataFrame(list(zip(titulos,imagenes)),columns = ['Título', 'imagenes'])
tabla.head()

```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Título</th>
      <th>imagenes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Patria</td>
      <td>http://static.fnac-static.com/multimedia/Image...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Norman y Mix</td>
      <td>http://static.fnac-static.com/multimedia/Image...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Más allá del invierno</td>
      <td>http://static.fnac-static.com/multimedia/Image...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Dragon Ball Serie roja nº 215/216</td>
      <td>http://static.fnac-static.com/multimedia/Image...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Escrito en el agua</td>
      <td>http://static.fnac-static.com/multimedia/Image...</td>
    </tr>
  </tbody>
</table>
</div>



-------------
