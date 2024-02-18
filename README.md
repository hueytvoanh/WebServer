# WebServer

Precondition:
    - Check firewall allow port 80
    - make sure duckdns sciprt ran and OK
1 Stop nginx 
      sudo systemctl stop nginx 
2 Run certbot command
      sudo certbot certonly --standalone --preferred-challenges http -d mqttnhatnhat1.duckdns.org
3 Copy 3 files from output to mosquitto cert file
    root@raspberrypi:/etc/letsencrypt/live/mqttnhatnhat.duckdns.org# cp cert.pem /etc/mosquitto/certs/
    root@raspberrypi:/etc/letsencrypt/live/mqttnhatnhat.duckdns.org# cp chain.pem /etc/mosquitto/certs/    
    root@raspberrypi:/etc/letsencrypt/live/mqttnhatnhat.duckdns.org# cp privkey.pem /etc/mosquitto/certs/
4 Start nginx again and check
    sudo systemctl start nginx

#DataBase
    sqlite3 iot.db
    sqlite> create table ats (atsID text, I1 numeric, V1 numeric, I2 numeric, V2 numeric, user text, rDatetime datetime);
