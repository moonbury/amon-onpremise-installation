# Amon On-Premise Installation
This document describes the steps i go through during the installation of [amon on-premise](https://docs.amon.cx/onpremise/).

For some reason, I have to install a on-premise amon server for the team. The documenation from amon is good but due to a development conflict/bug, here is the solution.

# Amon On-Premise Monitoring Server
Amon is a Django application, which stores all performance data in a MongoDB database. It is open source and you can find the code here.

# Hardware Requirement 
* 2GB of RAM, 4GB or More if you plan to monitor more then 20 machines
* 50GB - On average storing data for 1 server with the default 30 days retention takes around 500MB of disk space
* A dedicated (sub) domain if it is for public access
* Ubuntu 16.04 LTE (suggested)

# Setup Amon Server
1.	Install git. #sudo apt-get install git
2.	Download the easy setup scripts from https://github.com/bigcalm/amon-server. Go to /opt/ and use the command #git clone https://github.com/bigcalm/amon-server
3.	Go to the amon-server folder and edit the provision.sh. Change the email address in Line 7.
4.	Edit the amon.sh and replace the line 37 by  
```
# Install old working commit
cd /opt/amon && . /opt/amon/env/bin/activate && \
git checkout 773fdca && \
pip install -r /opt/amon/requirements.txt && \
python manage.py migrate

# Fix CSRF error (if you have it)
echo >> /opt/amon/amon/settings.py
echo 'CSRF_TRUSTED_ORIGINS = [host_struct.hostname,]' >> /opt/amon/amon/settings.py

# Update to master
cd /opt/amon && . /opt/amon/env/bin/activate && \
git checkout master && \
pip install -r /opt/amon/requirements.txt && \
python manage.py migrate && \
systemctl restart amon.service
```
*code from https://github.com/amonapp/amon/issues/211. 

5.	Assume for private use, run the #./provision.sh private
6.	If everything goes well, you should have amon, postfix, mongo and nginx installed and running.
7.	Edit the /opt/amon/amon/settings.py and the ALLOWED_HOST on line 214 and restart the nignx
8.	Edit the /opt/amon/amon/settings.py to the following and restart the postfix.
9.	Open a browser and go to the server ip address. You will see a create admin account screen.
10.	Create an account and try to login.
11.	Go to Setting > SMTP and try to send a testing email.

# Setup Amon Agent
Please consult https://docs.amon.cx/agent/#install for more detail.
* I cannot install via the installation url, so I manual install.
1.	Make sure you have python 2.7 installed in your monitor PC.
2.	Obtain the API Key from the server. 
3.	Follow the manual installation steps and replace the API key and the server IP.
4.	If success, you will see the agent is running and you can find it in the amon server.

# Acknowledgments
Big thank to [bigcalm](https://github.com/bigcalm/) for providing the installation scripts.

