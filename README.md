Docker environment for a Django 1.11 project
===========================================

# Add to your project

Simply, move these to the root folder in your project:

1. The `docker` folder containing python + django and a MySQL container config for it.
2. The `docker-compose.yml` file
3. The .env file

**Note:** Change PROJECT_NAME in the `.env` file

**Note:**: Make sure you modify python version & the requirements.txt for the django dockerfile.
 

# How to start django project (if not created)

## Create the images

Run `docker-compose up -d` to create the images and start the containers. Django's container will not start, don't worry.

## Create django project

Run `docker-compose run django django-admin.py startproject project_name .` to create project_name (don't forget the . in the end)

## Create application

Run `docker-compose run django python manage.py startapp app_name` to create app_name.

## Create the django image

Run `docker-compose up -d` to create the images again, this time, the django container will start.

# How to run

Dependencies:

  * Docker engine v1.13 or higher. Your OS provided package might be a little old, if you encounter problems, do upgrade. See [https://docs.docker.com/engine/installation](https://docs.docker.com/engine/installation)
  * Docker compose v1.12 or higher. See [docs.docker.com/compose/install](https://docs.docker.com/compose/install/)

Once you're done, simply `cd` to your project and run `docker-compose up -d`. This will initialise and start all the containers, then leave them running in the background.

## Services exposed outside your environment

You can access your application via **`127.0.0.1:8000`**, if you're running the containers directly.

## Hosts within your environment

You'll need to configure your application to use any services you enabled:

Service|Hostname |Port number
-------|---------|-----------
django |django   |8000
mysql  |mysql    |8306


# Development hints

Once you have it up and running, you can now edit your settings.

## Django settings.py:

  * Add your app_name in the installed apps

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    
    'app_name',
]
```


  * Configure your settings.py so your databases point to each database services by service name! For instance, for mySql connections 'HOST': 'mysql'

Change:
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```
To
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'docker_django_db',
        'USER': 'dbuser',
        'PASSWORD': 'dbpw',
        'HOST': 'mysql',
        'PORT': '3306',
        'TEST': {
            'NAME': 'docker_django_db_test',
        },
    }
}
```

## Django migrations:

Type `docker-compose exec django bash` in your bash to enter the python django bash.

Now run the migrations like so

`python manage.py makemigrations`

And 

`python manage.py migrate`

**Note:** If it's the first time you're running the migrations and your app is not working in your browser, try `docker-compose stop` and `docker-compose up -d` again. This is due to Django trying to execute the app without the ddbb created yet. It should now work.

## PyCharm configuration:
  * Run `docker-compose up -d` and in **Settings - Project: xxxx - Project interpreter** 
    * Add remote interpreter (cog)
      * type: Docker compose
      * service: django
  * **Run - Edit configurations** add a new **Django server**:
    * Host: 0.0.0.0
    * Port: 8000
    * Python interpreter: The remote python added in previous step...


# Docker compose cheatsheet

**Note:** you need to cd first to where your docker-compose.yml file lives.

  * Start containers in the background: `docker-compose up -d`
  * Start containers on the foreground: `docker-compose up`. You will see a stream of logs for every container running.
  * Stop containers: `docker-compose stop`
  * Kill containers: `docker-compose kill`
  * View container logs: `docker-compose logs`
  * Execute command inside of container: `docker-compose exec SERVICE_NAME COMMAND` where `COMMAND` is whatever you want to run. Examples:
    * Shell into the django container, `docker-compose exec django bash`
    * Open a mysql shell, `docker-compose exec mysql mysql -uroot -pCHOSEN_ROOT_PASSWORD`

# Docker general cheatsheet

**Note:** these are global commands and you can run them from anywhere.

  * To clear containers: `docker rm -f $(docker ps -a -q)`
  * To clear images: `docker rmi -f $(docker images -a -q)`
  * To clear volumes: `docker volume rm $(docker volume ls -q)`
  * To clear networks: `docker network rm $(docker network ls | tail -n+2 | awk '{if($2 !~ /bridge|none|host/){ print $1 }}')`