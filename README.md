# bash-r-aws-framework
bash and r scripts to create a simple framework for data analysis, file transformation, and file management

## background

I am comfrotable working with shell scripting and r. Although I understand there are other frameworks in place, I like to get into the nitty and gritty to make custom and basic level work that achieves a similar outcome. Overall, I use bash for file management on a linux server, while calling r to do the heavy lifting in analysis. I also love doing the server side work on aws servers to make use of the larger storage capacities and higher compute power.

## 1. case study: twitter mining and live analysis

Refer to https://github.com/justdevelopingstuff/twitter-unsupervised-topwords-sentiment-analysis. This was a collaborative effort to 1. scrape twitter; 2. mine tweets related to the EURO cup; 3. parse the data; 4. analyze the data in python/r; 5. produce graphs periodically and 6. push the results for display of live results. - all while on an aws server to run during the duration of the game.

## 2. jupyter notebooks for collaborative access to python and r data analysis

Jupyter notebooks is part of the rise in platforms for collaborative, reproducible, and readable code. I enjoy using these notebooks are they look great, produce professional looking documents, and allow for anyone you choose to access the notebook for viewing/editing.

### How to setup a jupyter r or python notebook with amazon aws

Basic instructions on how to setup a jupyter notebook on aws to do collaborative r or python coding - all while on the browser from any computer. The following are instructions adapted and tested with from the interwebs and also the original documentation.

#### 1. create an aws instance (free tier is great!)

You can check the documentation on how to setup an account for free and get started with free tier instances. https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html

#### 2. create and instance

I prefer using a clean vanilla ubuntu installation. Setup the security group to allow for tcp access to a desired port (more on that below) and from anywhere (unless you only want a specific range of ips to have access only). 

#### 3. ssh into the instance 

The instructions are also in the aws documentation.

#### 4. update system and install apps

```
sudo apt-get update;
sudo apt-get isntall tmux zsh wget vim htop;
```

For jupyter with the r-irkernel (for r), some other packges need to get r to work

```
sudo apt-get sudo apt-get install cc gfortran cmake unzip tcl tk build-essential checkinstall cvs subversion git-core mercurial libssl-dev libcurl4-openssl-dev libxml2-dev r-base vim zsh tmux htop
```

#### 5. install anaconda

This is to get jupyter and python kernel
E.g.
```
wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh
bash Anaconda3-4.4.0-Linux-x86_64.sh
source ~/.bashrc
```
#### 6. create a password hash

```
ipython
from IPython.lib import passwd
passwd() #once you are done, save the password hash
exit
```

#### 7. creating a conda env for package and version control.

Good practice to keep different environments. !!! As of 2017-06, I can't seem to get r working in a conda env. It seems to be related to the version of jupyter/anaconda/r within the packages of anaconda. If you want to use r, skip this step and don't make a conda env. See below.!!!

Install all of your packages you need for your notebook with python.
```
conda create --name some_env jupyter
source activate some_env
conda install numpy pandas opencv scikit-learn matplotlib tensorflow keras jupyter
```

#### 8. generate a jupyter config

This is where all the configurations will be stored.
Get the saved password hash ready.
Pick your favorite port.
For a standard configuration, you can use the following bash commands.
```
jupyter notebook --generate-config
echo "c = get_config()" >> ~/.jupyter/jupyter_notebook_config.py
echo "c.NotebookApp.password = u'YOUR_PW_HASH'" >> ~/.jupyter/jupyter_notebook_config.py
echo "c.NotebookApp.port = YOUR_FAV_PORT" >> ~/.jupyter/jupyter_notebook_config.py
```

#### 9. generate keys to allow the for https access into the jupyter notebook
```
cd
mkdir certs
cd certs
sudo openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
#answer all the questions
#echo "c.IPKernelApp.pylab = 'inline'" >> ~/.jupyter/jupyter_notebook_config.py #for the plots
echo "c.NotebookApp.certfile = u'/home/ubuntu/certs/mycert.pem'" >> ~/.jupyter/jupyter_notebook_config.py
echo "c.NotebookApp.ip = '*'" >> ~/.jupyter/jupyter_notebook_config.py
#echo "c.NotebookApp.open_browser = False" >> ~/.jupyter/jupyter_notebook_config.py #if you don't want ipython as default
```

#### 10. starting up the notebook
At this point you can check out my other repo on installing tmux. This allows your notebook to keep running even when you disconnect via ssh. Alternatively, you can use screen.

```
screen #press enter
jupyter notebook
```
At this point you have created a virtual shell. You can create multiple ones using ctrl-c and swithing uusing ctrl-n.
You can detach using ctrl-d. You can logout from your server at this point.

```
logout
```

#### extra. getting r to work

I have found the r-irkernel to die immediately. This only happened when I used a conda env for the r kernel and using the conda channel's latest r-base etc. Instead, to get it working (for now), I simply just skipped the env part.

After installing all the r packages, go into R

```
R
```

Within R, install all your usual packages you need. In addition, you need to download, install, and setup the r-irkernel to work with the jupyter notebook.

```
install.packages("devtools")
devtools::install_github('IRkernel/IRkernel')
IRkernel::installspec()
```
You can find more info on the r-irkernel github page.
