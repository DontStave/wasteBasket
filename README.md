# wasteBasket


###launch a markdown test


#### mongoDB installation

##### download
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu1404-3.4.6.tgz

##### unzip
tar -zxvf mongodb-linux-x86_64-ubuntu1404-3.4.6.tgz

sudo mv mongodb-linux-x86_64-ubuntu1404-3.4.6  /usr/local/mongodb

##### create path and grant authority for db and log 
sudo mkdir -p /usr/local/mongodb/data/db
 && sudo chmod 777 /usr/local/mongodb/data/db

sudo mkdir -p /usr/local/mongodb/log

sudo touch /usr/local/mongodb/log/mongodb.log

sudo chmod 777 /usr/local/mongodb/log/mongodb.log

##### self staring
- build up config file
    - ​sudo touch /usr/local/mongodb/mongodb.conf
    - ​sudo chmod 777 /usr/local/mongodb/mongodb.conf
    - ​mongodb.conf
      ```Bash
        dbpath=/usr/local/mongodb/data/db
        logpath=/usr/local/mongodb/log/mongodb.log
        logappend=true
        port = 27017
        journal=true
        fork = true
        quiet = true

      ``` 
- build up self staring shell
    - ​sudo touch /etc/init.d/mongodb
    -  sudo chmod 777 /etc/init.d/mongodb
    -  /etc/init.d/mongodb
```Bash
    #!/bin/sh

    ### BEGIN INIT INFO
    # Provides:     mongodb
    # Required-Start:
    # Required-Stop:
    # Default-Start:        2 3 4 5
    # Default-Stop:         0 1 6
    # Short-Description: mongodb
    # Description: mongo db server
    ### END INIT INFO

    . /lib/lsb/init-functions

    MONGOPID=`ps -ef|grep '/usr/local/mongodb/bin/./mongod'|grep -v grep|awk '{print $2}'`



    case "$1" in
      start)
         ulimit -n 3000
         log_begin_msg "Starting MongoDB server"
         if [ ! -z "$MONGOPID" ]; then
          echo "mongodb is already running"
         else
          sudo /usr/local/mongodb/bin/./mongod -f /usr/local/mongodb/mongodb.conf
         fi
         log_end_msg 0
      ;;
      stop)
         log_begin_msg "Stopping MongoDB server"
         if [ ! -z "$MONGOPID" ]; then
            kill -15 $MONGOPID
         else
            echo "mongodb is already stop"
         fi
         log_end_msg 0
      ;;
      status)
         if [ ! -z "$MONGOPID" ]; then
            log_begin_msg "running"
            log_end_msg 0
         else
            log_begin_msg "apparently not running"
            log_end_msg 0
            exit 0
         fi
      ;;
    esac

    exit 0
```
  - sudo update-rc.d mongodb defaults
  - usage: sudo service mongodb start|stop|status
  - vim /etc/rc.local  ADD 'sudo /usr/local/mongodb/bin/./mongod -f /usr/local/mongodb/mongodb.conf' BEFORE 'exit 0'

##### mongodb master slave cluster build up
sudo /usr/local/mongodb/bin/./mongod -f /usr/local/mongodb/mongodb.conf --master

sudo /usr/local/mongodb/bin/./mongod -f /usr/local/mongodb/mongodb.conf
 --slave --source 192.168.29.4:27017

sudo /usr/local/mongodb/bin/./mongod -f /usr/local/mongodb/mongodb.conf
 --slave --source IP:PORT
