# Docker_django

## PASO 1 "Creacion de imagen" (situados en Docker_django)
- Una vez descargado el repositorio e instalado docker en nuestro escritorio, procedemos a la construcción de la imagen mediante el comando
"sudo docker-compose build"

## PASO 2 "Creacion de los archivos de configuración del proyecto"
- Creada ya la imagen, procedemos a crear el proyecto con sus archivos de configuracion mediantes el comando(incluido el punto final):
"sudo docker-compose run --rm web django-admin startproject config ."

## PASO 3 "Modificacion para POSTGRESQL y variables de entorno"
- En tu directorio del proyecto, edita el archivo config/settings.py ya que vamos a trabajar con postgres y no con sqlite3 como por defecto viene en Django.
- ** A tener en cuenta que en el archivo .env.dev las variables se pueden sustituir por las que deseemos.

Sustituye el DATABASES = ... por lo siguiente: (importamos os)
# settings.py

import os

[...]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': os.environ.get('POSTGRES_NAME'),
        'USER': os.environ.get('POSTGRES_USER'),
        'PASSWORD': os.environ.get('POSTGRES_PASSWORD'),
        'HOST': os.environ.get('POSTGRES_HOST'),
        'PORT': os.environ.get('POSTGRES_PORT'),
    }
}

Tambien copiar y SUSTITUIR lo siguiente:
SECRET_KEY = os.environ.get("SECRET_KEY")
DEBUG = int(os.environ.get("DEBUG", default=0))
ALLOWED_HOSTS = os.environ.get("DJANGO_ALLOWED_HOSTS").split(" ")


## PASO 4 "Modificacion de aplicaciones instaladas"
Dentro del mismo archivo settings.py en el apartado "INSTALLED_APP" adjuntamos las aplicaciones que queramos incluir en el proyecto, en este caso
como no es un docker para una app nueva si no para dockerizar una app ya creada adjuntamos lo siguiente al final:

    'apps',
    'apps.mainapp',
    'apps.entry',
    'apps.analysis',
    'crispy_forms',
    'crispy_bootstrap5',

## PASO 5 "Adjuntar la app del repositorio en Bitbucker a la raiz DOCKER_DJANGO"
- Arrastramos la app "inquimepProject" con todas las subcarpetas y la pegamos en la raiz actual (DOCKER_DJANGO, NO en la carpeta creada config), una vez añadida, tenemos que sustuir el archivo "urls.py" de nuestra app 
en la carpeta config, para poder acceder a todas las rutas creadas en las vistas.

## PASO 6 "Sobreescritura del arhivo urls.py"
- Como hemos comentado, al ser una app ya creada y posteriormente dockerizada, tenemos que sustituir en el archivo "urls.py" del proyecto creado config lo siguiente para que nos funcione en la app creada:

from apps.mainapp import views

urlpatterns = [
    # user
    path('admin/', admin.site.urls),
    path('', views.login_page, name="login"),
    path('register/',views.register_page, name="register"),
    path('logout/', views.logout_user, name="logout"),
    path('user/', views.detail, name="detail"),
    path('add/',views.add_user, name="add"),
    path('delete/<int:id>', views.delete_user, name="delete"),
    path('update/<int:id>', views.update, name="update"),
    path('gestion', views.gestion, name="gestion"),
    path('profile', views.profile, name="profile"),
    # client
    path('create/', views.create_client, name="create"),
    path('client/', views.list_client, name="list_client"),
    path('view/', views.view_client, name="view_client"),
    path('edit/<int:id>', views.edit_client, name="edit_client"),
    path('delete_client/<int:id>', views.delete_client,name="delete_client"),
    path('profile_client/<int:id>', views.profile_client, name="profile_client"),
    # entry
    path('create_entry/', views.create_entry, name="create_entry"),
    path('entry/', views.list_entry, name="list_entry"),
    path('view_entry/', views.view_entry, name="view_entry"),
    path('edit_entry/<int:id>', views.edit_entry, name="edit_entry"),
    path('delete_entry/<int:id>', views.delete_entry,name="delete_entry"),
    path('profile_entry/<int:id>', views.profile_entry, name="profile_entry"),
    # analysis
    path('create_a/', views.create_a, name="create_a"),
    path('analysis/', views.list_a, name="list_a"),
    path('view_a/', views.view_a, name="view_a"),
    path('edit_a/<int:id>', views.edit_a, name="edit_a"),
    path('delete_a/<int:id>', views.delete_a,name="delete_a"),
    path('profile_a/<int:id>', views.profile_a, name="profile_a"),
    # gold
    path('create_g/', views.create_gold, name="create_g"),
    path('gold/', views.list_gold, name="list_g"),
    path('view_g/', views.view_gold, name="view_g"),
    path('edit_g/<int:id>', views.edit_gold, name="edit_g"),
    path('delete_g/<int:id>', views.delete_gold,name="delete_g"),
    path('profile_g/<int:id>', views.profile_gold, name="profile_g"),
    # silver
    path('create_s/', views.create_silver, name="create_s"),
    path('silver/', views.list_silver, name="list_s"),
    path('view_s/', views.view_silver, name="view_s"),
    path('edit_s/<int:id>', views.edit_silver, name="edit_s"),
    path('delete_s/<int:id>', views.delete_silver,name="delete_s"),
    path('profile_s/<int:id>', views.profile_silver, name="profile_s"),
]

## PASO 7 "Levantar el docker-compose"
- Una vez hechas todas las modificaciones, levantamos el contenedor mediante el comando:
docker-compose up

## PASO 8 "Creacion de superuser"
- En Django es imprescindible tener un superuser que administre el sistema o aplicación, para hacerlo abrimos un nuevo terminal y con 
el contener corriendo, escribimos los siguiente:
     - docker exec -it docker_django_web_1 (o el nombre de nuestro contenedor que en este caso es ese) /bin/sh
     - python3 manage.py createsuperuser (y añadimos los datos del superuser para acceder a la app)

## PARA ACCEDER A LA APP
- 127.0.0.1:8000