# Assignment

The goal of this particular case study was to build an infrastructure for Notejam application. Here I have chosen Flask framework for the implementation of the application.

This README.md file will explain the architecture of the infrastructure and the implementation. Also this documentation will explain in a way that this setup can be reproduced.

## Environment

This complete setup has been done on a macOS machine. 

	* Operating system: macOS Big Sur version 11.2.3
    * Docker-CE: version 20.10.5
    * minikube: version v1.18.1 
    * python3: version 3.8.1

## Implementation

The first step of this setup is to build the docker container for the application.

The complete folder structure will look like below

```
 ma.rahman@lenpc0wj7xt  ~/Desktop/nordcloud-task/sample/flask  tree
.
├── Dockerfile
├── README.rst
├── db.py
├── notejam
│   ├── __init__.py
│   ├── config.py
│   ├── forms.py
│   ├── models.py
│   ├── static
│   │   └── css
│   │       ├── style.css
│   │       └── tables.css
│   ├── templates
│   │   ├── _helpers.html
│   │   ├── base.html
│   │   ├── notes
│   │   │   ├── create.html
│   │   │   ├── delete.html
│   │   │   ├── edit.html
│   │   │   ├── form.html
│   │   │   ├── list.html
│   │   │   └── view.html
│   │   ├── pads
│   │   │   ├── create.html
│   │   │   ├── delete.html
│   │   │   ├── edit.html
│   │   │   ├── form.html
│   │   │   └── note_list.html
│   │   ├── user.html
│   │   └── users
│   │       ├── forgot_password.html
│   │       ├── settings.html
│   │       ├── signin.html
│   │       └── signup.html
│   ├── views.py
│   └── wsgi.py
├── notejam-deployment.yaml
├── notejam-pv.yaml
├── notejam-pvc.yaml
├── requirements.txt
├── run.sh
├── runserver.py
└── tests.py

```
