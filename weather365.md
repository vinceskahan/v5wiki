If you are new, please register your station first at https://stations.weather365.net/login/ ! Please follow the instructions carefully and enter the exact longitude, latitude and alt for your station. You will get a unique station ID by mail.

a) Install weather365-uploader

On raspberry / linux Systems open a terminal (ctrl+alt+F1) or SSH session and as the user 'pi’ run: This installation-method works for Debian- and Python-installed weewx-systems with standard path.

bash <(curl -sL https://stations.weather365.net/wxupdates/update-weewx.sh)

(If you’re running the installation over SSH, running the installation through a screen or tmux session is highly recommended.)

Assuming everything went well, your data will be sent to stations.weather365.net and the first dataset is visible after approx 5 or 10 minutes.

b) manual install weather365-uploader

Download

https://stations.weather365.net/wxupdates/Downloads/weather365-3.0.zip

Installation raspberry-pi or Linux

Login on your raspberry / Linux locally or remote . On raspberry try it as Standard-User „pi“.

This install describes a Debian based install.

If you´re on a python2 system pleas install package python-future at first ! 
ex. install for Linux/raspberry systems stretch / buster 
sudo apt-get update 
sudo apt-get install python-future

If you´re on a python3 system pleas install package python3-future at first ! 
ex. install for Linux/raspberry systems stretch / buster 
sudo apt-get update 
sudo apt-get install python3-future

1. Fetch the Uploader ( using Terminal Window )

wget https://stations.weather365.net/wxupdates/Downloads/weather365-3.0.zip

2. Install the update

sudo wee_extension --install weather365-3.0.zip

3. extend weewx.conf

Add here -> [StdRESTful] weewx.conf …

sudo nano /etc/weewx/weewx.conf

Find [StdRESTful] and add your station ID

[[Weather365]]

stationid = xxxxxxxxxx

password = 14991234912

SAVE with CTRL-X

4. Restart weewx ( Sometimes you have to restart twice )

sudo /etc/init.d/weewx stop

sudo /etc/init.d/weewx start

Copyright [2018] [Frank Bandle / Weather365]

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License.

You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS,

WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.

See the License for the specific language governing permissions and limitations under the License.