# Desafío Meli acortador de URL
Para este proyecto ocupamos Docker Compose pues simplifica enormemente la configuración, gestión y escalabilidad de aplicaciones.

##### Cómo correr Docker de forma local

* `docker-compose up -d`

##### Reconstruir Docker image

* `docker-compose up --build `

##### Correr los test usando pytest

* `docker exec -it <id_contenedor>  bash`

##### Hostnames para acceder a la app
* Local: http://127.0.0.1:8000

### Arquitectura de Acortador de URL:
Para construir nuestra arquitectura de acortador de URL primero definimos los  principales endpoints de nuestra API. Para nuestra API usé FASTAPI pues es un framework que permite manejar solicitudes de manera extremadamente rápida y eficiente.

##### POST
```python
@router.post("/shorten")
create_shorten_url(
    url_input: URLInput,
    url_shortener_service: URLShortenerService = Depends(get_url_shortener_service)
    ) 
```
Este endpoint se encarga de crear en base de datos y retornar la url corta.
Los parámetros son url_input que es de donde obtendremos nuestra url original y que es del tipo URLInput. URLInput es un BaseModel, que es una clase proporcionada por Pydantic, una biblioteca de validación de datos, que se utiliza para definir y validar datos de entrada y salida de manera estructurada. Por otro lado, tenemos  
el decorador Depends indica a FastAPI que debe llamar a la función get_url_shortener_service para obtener la instancia de URLShortenerService que es un servicio que interactua con la base de datos.


##### GET
```python
    @router.get("/{url_hash}")
    async def retrieve_url(
    url_hash: str,
    url_shortener_service: URLShortenerService = Depends(get_url_shortener_service)
    ):
```
Con este endpoint se puede con la url corta redirigir a la original. Este endpoint tiene como parámetros url_hash y url_shortener_service que es un servicio que nos va ayudar a obtener a url original desde la base de datos.

##### PUT
```python
    @router.put("/update_url_hash/{url_hash}")
    async def update_short_url(
    url_hash:str,
    url_input: URLInputUpdate,
    url_shortener_service: URLShortenerService = Depends(get_url_shortener_service)
    ):
```

Con este endpoint se puede hacer un update de la url de destino. Este endpoint tiene como parámetros url_hash, url_shortener_service y URLInputUpdate que es un base model cuyo schema pide las componentes de una url como el procotolo, dominio,etc por lo que este endpoint puede cambiar cualquier componente de la url de destino.
#### Diseño de la base de datos
Para esta implementación no implementé una tabla usuarios por la simplicidad del sistema, pero se puede haber definido para el caso en que queramos que el sistema sea privado o que al hacer la request podamos guardar información del usuario.

Schemas de mis tablas:
   <img width="659" alt="Captura de Pantalla 2024-05-17 a la(s) 18 04 45" src="https://github.com/ValeDondoP/meli-url-shortener/assets/80803286/d2cc6037-238b-4aab-8c88-244f472c2a52">


En este diseño consideré que las URL se pueden desactivar y también que se pueda saber el número de veces que se ha hecho click en la URL


