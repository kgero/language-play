# Language Play website

## Set up

Instructions for setting up a single app: [How To Serve Flask Applications with uWSGI and Nginx on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-uswgi-and-nginx-on-ubuntu-18-04)

### Virtualenv

Set up a local virtualenv using the command `python3.7 -m venv env/` then activate it with `source env/bin/activate` and install the requirements. Check your python version Check that the app will run locally with `python server.py`. Might need to manually set up a `.env` file if API keys are required.

### Additional files for the project directory


* `wsgi.py` in the project directory

```
from server import app

if __name__ == "__main__":
    app.run()
```

* `projectname.ini` in the project directory

```
[uwsgi]
mount = /projectname=wsgi.py
callable = app

manage-script-name = true

master = true
processes = 2

socket = projectname.sock
chmod-socket = 660
vacuum = true

die-on-term = true
```

### Files elsewhere on server

* a `.service` file in `/etc/systemd/system/` (need to use `sudo` for this file)

```
[Unit]
Description=uWSGI instance to serve projectname
After=network.target

[Service]
User=katy
Group=www-data
WorkingDirectory=/home/katy/projectname
Environment="PATH=/home/katy/projectname/env/bin"
ExecStart=/home/katy/projectname/env/bin/uwsgi --ini projectnametext.ini

[Install]
WantedBy=multi-user.target
```

* to have its location added to `/etc/nginx/sites-available/language-play`

### Check on app

Use `systemctl` to start process and check for errors. Some useful commands:

```
sudo systemctl start projectname
sudo systemctl enable projectname
sudo systemctl status projectname
sudo systemctl restart projectname
```