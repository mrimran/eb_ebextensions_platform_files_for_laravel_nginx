# Elastic beanstalk .ebextensions and .platform files for laravel and nginx

Necassary files to run laravel project correctly with elastic beanstalk and nginx.

## How to use it

- Copy .ebextensions and .platform directories in your laravel's project root.
- Open `.ebextensions/00_settings.config` and fill your elastic beanstalk's configurations, for instance database and URL.
- Open `.platform/nginx/conf.d/my-vhost.conf` and update the domain names that you use (not needed in case you want to run your application on default URL that elastic beanstalk have created for you).

## Optional but helpful

- Following commands can be used to setup permissions or this method can be used to create many other commands

```
container_commands:
  03_make_storage_writeable:
    command: "chmod -R 775 storage"
    cwd: /var/app/staging
  04_make_bootstrap_cache_writable:
    command: "chmod -R 775 bootstrap/cache"
    cwd: /var/app/staging
```

 - One can use below to create shell scripts that will run post deployment

```
files:
  "/opt/elasticbeanstalk/hooks/appdeploy/post/99_make_storage_writable.sh":
    mode: "000755"
    owner: root
    group: root
    content: |
      #!/usr/bin/env bash
      echo "Making /storage writeable..."
      chmod -R 775 /var/app/current/storage

      if [ ! -f /var/app/current/storage/logs/laravel.log ]; then
          echo "Creating /storage/logs/laravel.log..."
          touch /var/app/current/storage/logs/laravel.log
          chown webapp:webapp /var/app/current/storage/logs/laravel.log
      fi

      if [ ! -d /var/app/current/public/storage ]; then
          echo "Creating /public/storage symlink..."
          ln -s /var/app/current/storage/app/public /var/app/current/public/storage
      fi
```

## Install some library that exsists in the OS packages

```
packages: 
  yum:
    epel-release: []
    mod24_ssl: []
```
