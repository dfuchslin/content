---
id: 462
title: 'Upload WH1080 weather measurements to Weather Underground'
date: '2016-09-03T22:42:03+02:00'
author: David
layout: revision
guid: 'https://david.gyttja.com/2016/09/03/427-revision-v1/'
permalink: '/?p=462'
---

The next step after collecting weather statistics from <a href="https://david.gyttja.com/2016/02/29/use-an-old-wh1080-weather-station-with-raspberry-pi/">my WH1080 weather station</a> was to upload the results to Weather Underground. As it turns out, it was quite simple to enable a wunderground renderer in wfrog. Here's what I did.


sudo easy_install weather
sudo easy_install pyweather
python -v
easy_install pyweather
sudo easy_install pyweather
sudo apt-get install python3
which python
ls -l /usr/bin/python
sudo nano /usr/lib/wfrog/wfdriver/config/embedded.yaml
sudo wfrog --customize
cd /etc/wfrog/wfcommon/config/
ls -l
cd ..
cd ../wfdriver/
ls -l
cd config/
ls -l
nano embedded.yaml
cd ..
ls -l
grep -R "wunderground" *
sudo nano wfrender/config/embedded.yaml
sudo nano wfrender/config/wfrender.yaml
uptime
sudo service wfrog restart
dmesg
cat /var/log/wfrog.log
tail -f /var/log/wfrog.log
tail -1000f /var/log/wfrog.log
sudo apt-get install wfrog
sudo nano /usr/lib/wfrog/wfdriver/station/wh1080.py
tail -1000f /var/log/wfrog.log
sudo service wfrog restart
tail -1000f /var/log/wfrog.log
sudo apt-get install python2.6
sudo apt-get install python2
sudo apt-get install python3.4
python --version
sudo update-alternatives --list python
ls -l /usr/bin/python
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.4 2
pythong --version
python --version
sudo service wfrog restart
sudo service wfrog status
tail -1000f /var/log/wfrog.log
date
tail -1000f /var/log/wfrog.log
sudo easy_install cheetah
sudo easy_install lxml
sudo easy_install pip
sudo easy_install Distribute
sudo easy_install -U Distribute
sudo reboot
exit
tail -1000f /var/log/wfrog.log
sudo service wfrog start
sudo service wfrog status
python --version
sudo pip -U upgrade
sudo easy_install cheetah
pip install --upgrade setuptools
sudo pip install --upgrade setuptools
sudo apt-get install pip
ls -l /usr/local/bin/pip
cat /usr/local/bin/pip
python -m pip -V
whichi python
which python
sudo update-alternatives --list python
sudo update-alternatives --config python
reboot
sudo reboot
tail -1000f /var/log/wfrog.log
sudo easy_install weather
sudo easy_install PyWeather
sudo easy_install pyweather
wget https://github.com/cmcginty/PyWeather/archive/v0.9.tar.gz
cd
ls -l
tar xzvf v0.9.tar.gz
sudo apt-get install pyweather
sudo apt-get install py-weather
ls -l
cd PyWeather-0.9/
ls -l
less README
less INSTALL.txt
sudo make install
sudo service wfrog restart
tail -1000f /var/log/wfrog.log
cd ..
tail -1000f /var/log/wfrog.log
top
tail -1000f /var/log/wfrog.log