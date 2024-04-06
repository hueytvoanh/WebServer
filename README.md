# WebServer
https://github.com/futureshocked/RaspberryPiFullStack_Raspbian

systemctl restart nginx

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
    sqlite> create table ats (id INTEGER PRIMARY KEY AUTOINCREMENT, atsID text, I1 numeric, V1 numeric, I2 numeric, V2 numeric, userID text, rDatetime datetime); 
Check existing tables:
    sqlite> .tables
Check data structure of the database:
    sqlite> .schema ats OR
    sqlite> PRAGMA table_info(ats);
Insert data to table:
    sqlite> insert into ats values (NULL, "ATS1", 0.5, 48.3, 1.7, 48.9, "admin", datetime(CURRENT_TIMESTAMP));
Read data from table:
    sqlite> select * from ats;
Delete table from database:
    DROP TABLE IF EXISTS ats;
Delete all recored data:
    sqlite> delete * from ats;
#NODE_RED
Insert sqlite node to NODE RED
Add database to the node: iot.db. After deploy below the node should has green led indicator. 
Change the db to read-write mode: sudo chmod 777 iot.db

NODE RED function
var date = new Date();
var timestamp = date.getTime();
timestamp = timestamp + (3600 * 1000);

msg.topic = "INSERT INTO ats VALUES (NULL, \"ATS1\", 0.5, 48.3, 1.7, 48.9, \"admin\", datetime(\"now\"))";

return msg;

Python code get data from sqlite database:
    conn 			    = sqlite3.connect('/var/www/lab_app/lab_app.db')
	curs 			    = conn.cursor()
	curs.execute("SELECT * FROM temperatures WHERE rDateTime BETWEEN ? AND ?", (from_date_utc.format('YYYY-MM-DD HH:mm'), to_date_utc.format('YYYY-MM-DD HH:mm')))
	temperatures 	    = curs.fetchall()
	curs.execute("SELECT * FROM humidities WHERE rDateTime BETWEEN ? AND ?", (from_date_utc.format('YYYY-MM-DD HH:mm'), to_date_utc.format('YYYY-MM-DD HH:mm')))
	humidities 		    = curs.fetchall()
	conn.close()

	return [temperatures, humidities, timezone, from_date_str, to_date_str]

Python code for choosing column data in a table of database:
    for record in temperatures:
		local_timedate = arrow.get(record[0], "YYYY-MM-DD HH:mm").to(timezone)
		time_series_adjusted_tempreratures.append(local_timedate.format('YYYY-MM-DD HH:mm'))
		time_series_temprerature_values.append(round(record[2],2))

HTML template
     1. lab_temp.html
     2. no_sensor.html
     3. lab_env_db.html

sqlite3 ats_iot.db     
create table temperatures (rDatetime datetime, sensorID text, temp numeric);
create table humidities (rDatetime datetime, sensorID text, hum numeric);

insert into temperatures values (datetime(CURRENT_TIMESTAMP),"1",25.10);
insert into temperatures values (datetime(CURRENT_TIMESTAMP),"1",35.10);
insert into temperatures values (datetime(CURRENT_TIMESTAMP),"1",45.10);
insert into temperatures values (datetime(CURRENT_TIMESTAMP),"1",55.10);

insert into humidities values (datetime(CURRENT_TIMESTAMP),"1",51.10);
insert into humidities values (datetime(CURRENT_TIMESTAMP),"1",61.10);
insert into humidities values (datetime(CURRENT_TIMESTAMP),"1",71.10);
insert into humidities values (datetime(CURRENT_TIMESTAMP),"1",81.10);

commit;

select * from temperatures;
select * from humidities;

cd /var/www/lab_app
hello.py control Flask app 
sudo systemctl start nginx
systemctl restart emperor.uwsgi.service
