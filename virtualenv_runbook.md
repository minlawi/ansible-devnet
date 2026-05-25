# Setting up virtualenv for ansible-community package

- sudo apt install python3-virtualenv
- sudo apt install python3-pip

- mkdir py_venv
- cd py_venv/
- virtualenv -p python3.10 ansible-community
- source ansible-community/bin/activate
- pip3 list 

(ansible-community) lawi@dev:~/py_env$ pip3 list
Package    Version
---------- -------
pip        22.0.2
setuptools 59.6.0
wheel      0.37.1

(ansible-community) lawi@dev:~/py_env$ pip install ansible
(ansible-community) lawi@dev:~/py_env$ pip list

- ansible --version
- ansible-community --version

# Setting up virtualenv for ansible-core package

- virtualenv -p python3.10 ansible-core
- source ansible-core/bin/activate
- pip3 list

(ansible-core) lawi@dev:~/py_env$ pip install ansible-core
(ansible-core) lawi@dev:~/py_env$ pip list

- ansible --version
- ansible-community --version # Expected command not found