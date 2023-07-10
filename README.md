# Ejercicio Replit

1. Inscríbete (con tu equipo) en replit https://replit.com
2. Crea una cuenta en ElephantSQL https://www.elephantsql.com


## Iniciar replit

Crea un nuevo replit en lenguaje Python como lo hizo el profesor.

Luego crea una aplicación web hello usando la IA, si eso no funciona entonces en el archivo `main.py` agrega esto:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
  return "<p>Hola, Mundo!</p>"
  
app.run(host='0.0.0.0', port=8080)
```

## Agregar initdb.py

Crea una instancia en ElephantSQL para tu base de datos de peliculas.

Obten el url de conexión que aparece en el campo `URL` en la página `DETAILS`de tu instancia.

Luego agrega en REPLIT el archivo initdb.py con este contenido:

```python
import os
import psycopg2

conn = psycopg2.connect("COLOCA ACA LA URL QUE OBTUVISTE DE ELEPHANT")

# Open a cursor to perform database operations
cur = conn.cursor()

# Execute a command: this creates a new table
cur.execute('DROP TABLE IF EXISTS movies;')
cur.execute('CREATE TABLE movies (id serial PRIMARY KEY,'
                                 'title VARCHAR (150) NOT NULL,'
                                 'year INT NOT NULL,'
                                 'director VARCHAR (50) NOT NULL,'
                                 'rating INT,'
                                 'review TEXT,'
                                 'date_added DATE DEFAULT CURRENT_TIMESTAMP);'
                                 )

# Insert data into the table

cur.execute('INSERT INTO movies (title, year, director, rating, review)'
            'VALUES (%s, %s, %s, %s, %s)',
            ('Dune', 2021, 'Dennis Villeneuve', 4, 'Great adaption of a cassic sci-fi novel'))
cur.execute('INSERT INTO movies (title, year, director, rating, review)'
            'VALUES (%s, %s, %s, %s, %s)',
            ('Elvis', 2022, 'Baz Luhrmann', 4, 'Hype stylized biopic of the King'))


conn.commit()

cur.close()
conn.close()
```

Asegurate que el archivo `pyproject.toml` contenga esta sección

```
[tool.poetry.dependencies]
python = ">=3.8.0,<3.9"
numpy = "^1.22.2"
replit = "^3.2.4"
Flask = "^2.2.0"
urllib3 = "^1.26.12"
SQLAlchemy = "^1.4.41"
flask-sqlalchemy = "^3.0.0"
psycopg2-binary = "^2.9.4"
```

Ejecuta el script en la ventana shell:

```
python initdb.py
```


Verifica que la base se creó en Elephant

### Pregunta 1:

¿Cuál o cuales de los 12 factores hemos usado al crear initdb?

## Crear un secret

Modifica el script `initdb.py` para que use el secret `CONNECTION_STRING`.

Debes crear este secret de la forma que te mostró el profesor.

En este secret debes colocar la URL del paso anterior.

TIP:

```
conn_string = os.environ['CONNECTION_STRING']
conn = psycopg2.connect(conn_string)
```


### Pregunta 2:

¿Cuáles de los 12 factores hemos usado al usar los secrets?


## Modificar la url index para mostrar nuestros datos

Modifica en `main.py` agregando esta función antes de la función `index()`:

```
from flask import Flask
import psycopg2
import os

app = Flask(__name__)

def get_db_connection():
  conn_string = os.environ['CONNECTION_STRING']
  conn = psycopg2.connect(conn_string)
  return conn

@app.route('/')
def index():
  conn = get_db_connection()
  cur = conn.cursor()
  cur.execute('SELECT * FROM movies;')
  movies = cur.fetchall()
  response = ""
  for movie in movies:
    response += "<div>"
    response += "<h3>" + str(movie[0]) + " - " + movie[1] + "</h3>"
    response += "<i><p>(" + str(movie[2])+ ")</p></i>"
    response += "<i><p>Director: " + movie[3] + "</p></i>"
    response += "<p>" + movie[5]+  "</p>"
    response += "<i><p>Rating: " + str(movie[4]) +" / 5</p></i>"
    response += "<i><p>Added" + str(movie[6]) + "</p></i>"
  cur.close()
  conn.close()
  return response

app.run(host='0.0.0.0', port=8080)

```

Ejecuta usando el boton play de REPLIT

