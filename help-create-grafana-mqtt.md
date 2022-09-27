1) Install nginx
2) install certbot
3) get cert for your site via nginx certbo plugin
4) install influxdb

    apt install curl gnupg gnupg1 gnupg2 net-tools software-properties-common

    sudo curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -

    source /etc/lsb-release
    echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list

    sudo apt update
    sudo apt install influxdb -y

    sudo systemctl start influxdb
    sudo systemctl enable influxdb

    netstat -plntu | grep '8088\|8086'
    
    ### Create User and Database to store the Data from Telegraf Agent.
    influx

    create database telegraf
    create user telegraf with password 'myP@ssw0rd'

    show databases
    show users
   
5) install telegraf
   
    sudo apt install telegraf -y

    sudo systemctl start telegraf
    sudo systemctl enable telegraf

    sudo systemctl status telegraf

    ### Configure Telegraf
    In this step, we will configure the Telegraf to use basic input plugins for 
    collecting system metric of the server and using the influxdb as the output plugin.

    cd /etc/telegraf/telegraf.d/

    touch my_config_telegraf.conf
    nano my_config_telegraf.conf
    
# Global Agent Configuration
[agent]
  hostname = "Grafana"
  flush_interval = "10s"
  interval = "10s"


          # Input Plugins
          [[inputs.cpu]]
              percpu = true
              totalcpu = true
              collect_cpu_time = false
              report_active = false
          [[inputs.disk]]
              ignore_fs = ["tmpfs", "devtmpfs", "devfs"]
          [[inputs.io]]
          [[inputs.mem]]
          [[inputs.net]]
          [[inputs.system]]
          [[inputs.swap]]
          [[inputs.netstat]]
          [[inputs.processes]]
          [[inputs.kernel]]

          # Output Plugin InfluxDB
          [[outputs.influxdb]]
            database = "telegraf"
            urls = [ "http://127.0.0.1:8086" ]
            username = "telegraf"
            password = "myP@ssw0rd"

          #Telegraf provides telegraf command to manage the configuration, 
          #including generate the configuration itself, run the command as below.
          
6) install grafana
  

      wget -q -O - https://packages.grafana.com/gpg.key | apt-key add -

      add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"

      apt-get update -y
      apt-get install grafana -y

      systemctl daemon-reload
      systemctl enable --now grafana-server
      systemctl start grafana-server
      systemctl status grafana-server
      
      ## Login to grafana
      http://192.168.178.37:3000
      Username: admin
      Password: admin

      ##
      add data source (influxDB)
        urls = [ "http://127.0.0.1:8086" ]
        database = "telegraf"
        username = "telegraf"
        password = "myP@ssw0rd"
        Type = GET
        
       
