### Pre-requesties setup before practicing CLI PARSER

```bash
# Create a virtual environment named 'pyATS' using Python 3.10
# This isolates dependencies for this project from system Python
virtualenv -p python3.10 pyATS

# Activate the virtual environment (run this before installing packages)
# source pyATS/bin/activate

# Install Ansible - automation tool for configuration management
pip install ansible

# Install ansible-pylibssh - SSH plugin for Ansible using libssh library
# Provides faster SSH connections compared to paramiko
pip install ansible-pylibssh

# Install pyATS - Python Automation Test Framework by Cisco
# Core library for network device testing and automation
pip install pyats

# Install Genie - extends pyATS with higher-level abstractions
# Provides parsers, libraries, and utilities for network automation
pip install genie

# Install ntc-templates - text parser templates for network devices
# Enables structured output parsing from show commands
pip install ntc-templates
```