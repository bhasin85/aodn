## Setup systest environment locally 
```
~/AODN/chef$ vagrant up --provision 1-nec-hob.emii.org.au
~/AODN/chef$ vagrant ssh 1-nec-hob.emii.org.au
```

## Run ruby script to recreate harvester
```
vagrant@vagrant-6-aws-syd:/var/chef/mnt/ebs/tomcat7/geonetwork_portal$ sudo /opt/chef/embedded/bin/ruby geonetwork-config-manager.rb -g http://172.28.128.3:8081/geonetwork -d "/mnt/ebs/geonetwork_portal" -c geonetwork-config-geonetwork.json -u admin -p Eqfumh9IbnXg97fbtrX6G9F
```

## Run harvester "IMOS Catalogue"
## Copy geoserver local war build using -  mvn clean install
```
vagrant@vagrant-1-nec-hob:/mnt/ebs/tomcat7/geoserver/webapps$ sudo cp /mnt/source-code/geoserver-build/src/main/target/geoserver.war .
```

## Copy aodn-portal war build using - grails war
```
vagrant@vagrant-1-nec-hob:/mnt/ebs/tomcat7/portal_imos123/webapps$ sudo cp /mnt/source-code/aodn-portal/target/aodn-portal-4.23.3-production.war .
```

## Override ROOT.war
```
vagrant@vagrant-1-nec-hob:/mnt/ebs/tomcat7/portal_imos123/webapps$ sudo mv aodn-portal-4.23.3-production.war ROOT.war
```

## Restart portal and geonetwork
```
vagrant@vagrant-1-nec-hob:/mnt/ebs/tomcat7/portal_imos123/webapps$ sudo service tomcat_geonetwork_portal restart
vagrant@vagrant-1-nec-hob:/mnt/ebs/tomcat7/portal_imos123/webapps$ sudo service tomcat_portal_imos123 restart
```

## In geonetwork - Allow editing on harvested records	
//For metadata record update protocol to "OGC:WPS--gogoduck"
// url http://172.28.128.3:8080/geoserver/ows

###### Update database url and credentials - url="jdbc:postgresql://dbprod.emii.org.au:5432/harvest
```
vagrant@vagrant-1-nec-hob:/mnt/ebs/tomcat7/geoserver/conf/Catalina/localhost$ sudo nano geoserver.xml 
```

## Backup geonetwork database
```
mkdir temp
sudo chmod 777 temp/
sudo -u postgres pg_dump -Fc geonetwork_aodn | less
sudo -u postgres pg_dump -Fc geonetwork_aodn > temp/geonetwork.dump
```

## Copy database dump to local
```
rsync -avzP 6-aws-syd.emii.org.au:/mnt/ebs/backups/backups/pgsql .
```

## Import database
```
sudo -u  postgres pg_restore /home/AODN/temp/ankit/geonetwork_imos.dump -C -d postgres -x
sudo -u postgres psql postgres

-x    dont' restore other permission
-C    means create database - eg. runs 'create database ...'    good because means get correct text unicode type eg. au utf, rather than default.
```

## Test run xslt
```
java -jar ./target/mafia-1.0.0.jar \
  -url jdbc:postgresql://localhost/geonetwork_imos -user postgres -pass postgres \
  -uuid 4402cb50-e20a-44ee-93e6-4728259250d2 \
  -stdout
  
java -jar ./target/mafia-1.0.0.jar \
  -url jdbc:postgresql://localhost/geonetwork -user geonetwork -pass geonetwork \
  -all \
  -transform xslt/367_transform_systest_urls/transform.xsl \
```

## Update S3 objects metadata recursively
```
aws s3 cp s3://aodn-github-backups/ s3://aodn-github-backups/ --recursive --metadata-directive REPLACE --cache-control no-cache
```
