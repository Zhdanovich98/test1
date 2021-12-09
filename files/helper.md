## Helper  ##

#### 1. No module named 'ansible' ####
```
Traceback (most recent call last):
  File "/usr/bin/ansible-playbook", line 34, in <module>
    from ansible import context
ModuleNotFoundError: No module named 'ansible'
```
 If you get this error, you didnt install ansible in virtual env. You should install ansible with requirements.txt:

```sh
pip install -r ./files/requirements.txt
```

 Or install ansible without requirements.txt:

```sh
pip install ansible==2.9.6
```

#### 2. FileNotFoundError: python ####
```
FileNotFoundError: [Errno 2] No such file or directory: '/usr/bin/python3.7'
```
If you get this error, you indicated python version, which not found on your host machine. Or you indicated false path to python<version>.
