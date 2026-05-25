# Setting up virtualenv for ansible-community package

- sudo apt install python3-virtualenv
- sudo apt install python3-pip

- mkdir py_venv
- cd py_venv/
- virtualenv -p python3.10 ansible-community
- source ansible-community/bin/activate

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