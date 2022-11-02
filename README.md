## Setup MongoDB with a vaild key in Machine 2

- Create a key file - sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D68FA50FEA312927
- `echo "deb https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list`
- Update and Upgrade - `sudo apt-get update -y && sudo apt-get upgrade -y`
- Install MongoDB - `sudo apt-get install -y mongodb-org=3.2.20 mongodb-org-server=3.2.20 mongodb-org-shell=3.2.20 mongodb-org-mongos=3.2.20 mongodb-org-tools=3.2.20`
- Check if its running - sudo systemctl status mongod
  
## Step 3: Configure the mongod.conf to allow access to everyone

- Open the config file - `sudo nano /etc/mongod.conf`
- Check if its listening on `port: 27017`
- Change the config file to allow access to everyone by changing bindIp to 0.0.0.0
  
## Restart the service

- Restart - sudo systemctl restart mongod
- Enable - sudo systemctl enable mongod
- Status - sudo systemctl status mongod

## Create a Env variable in the App VM to point to the MongoDB VM (Connect DB VM & App VM)

- Add the variable to the .bashrc file and source it
- `export DB_HOST=mongodb://192.168.10.150:27017/posts`
- source .bashrc
- Check if the variable is set echo $DB_HOST