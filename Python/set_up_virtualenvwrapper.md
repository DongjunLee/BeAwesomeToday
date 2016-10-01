# Set up and use a virtual python environment in Ubuntu.

##With virtualenvwrapper (user friendly wrappers for the functionality of virtualenv)

### Install pip

Install pip for Python 2 with

```bash
sudo apt-get install python-pip
```

or for Python 3

```bash
sudo apt-get install python3-pip
```

**Optional: Turn on bash autocomplete for pip**

Run

```bash
pip completion --bash >> ~/.bashrc
```

and run ```source ~/.bashrc``` to enable.

### Install virtualenv

Install **virtualenv** with

```bash
sudo apt-get install virtualenv
```

### Use pip to install virtualenvwrapper

The reason we are also installing [virtualenvwrapper](http://pypi.python.org/pypi/virtualenvwrapper) is because it offers nice and simple commands to manage your virtual environments. Because we want to avoid ```sudo pip``` we install ```virtualenvwrapper```locally (by default under ```~/.local```):

```bash
pip install --user virtualenvwrapper
```

or for Python 3

```bash
pip3 install --user virtualenvwrapper
```

and

```bash
echo "export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3" >> ~/.bashrc
```

### Setup virtualenv and virtualenvwrapper

First we export the WORKON_HOME variable which contains the directory in which our virtual environments are to be stored. Let's make this ~/.virtualenvs

```bash
export WORKON_HOME=~/.virtualenvs
```
now also create this directory

```bash
mkdir $WORKON_HOME
```
and put this export in our ```~/.bashrc``` file so this variable gets automatically defined

```bash
echo "export WORKON_HOME=$WORKON_HOME" >> ~/.bashrc
```
To use ```virtualenvwrapper``` we need to import its functions in our ```~/.bashrc```

```bash
echo "source ~/.local/bin/virtualenvwrapper.sh" >> ~/.bashrc
```
We can also add some extra tricks like the following, which makes sure that if pip creates an extra virtual environment, it is also placed in our ```WORKON_HOME``` directory:

```bash
echo "export PIP_VIRTUALENV_BASE=$WORKON_HOME" >> ~/.bashrc 
```
**Source ~/.bashrc to load the changes**

```bash
source ~/.bashrc
```

**Test if it works**

Now we create our first virtual environment. The -p argument is optional, it is used to set the Python version to use; it can also be python3 for example.

```bash
mkvirtualenv -p python2.7 test
```

You will see that the environment will be set up, and your prompt now includes the name of your active environment in parentheses. Also if you now run

```bash
python -c "import sys; print sys.path"
```

you should see a lot of /home/user/.virtualenv/... because it now doesn't use your system site-packages.

You can deactivate your environment by running

```bash
deactivate
```

and if you want to work on it again, simply type

```bash
workon test
```

Finally, if you want to delete your environment, type

```bash
rmvirtualenv test
```

### Reference
- http://askubuntu.com/questions/244641/how-to-set-up-and-use-a-virtual-python-environment-in-ubuntu