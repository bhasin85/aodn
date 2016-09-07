# Point local geoserver to production database

## context.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
    <New id="defaultDs" class="org.eclipse.jetty.plus.jndi.Resource">
        <Arg></Arg>
        <Arg>jdbc/default</Arg>
        <Arg>
            <New class="org.apache.commons.dbcp.BasicDataSource">
                <Set name="driverClassName">org.postgresql.Driver</Set>
                <Set name="url">jdbc:postgresql://localhost:5432/geoserver</Set>
                <Set name="username">geoserver</Set>
                <Set name="password">geoserver</Set>
                <Set name="validationQuery">SELECT 1</Set>
                <Set name="maxActive">10</Set>
                <Set name="maxIdle">5</Set>
                <Set name="maxWait">-1</Set>
                <Set name="testOnBorrow">true</Set>
                <Set name="testWhileIdle">true</Set>
                <Set name="testOnReturn">true</Set>
                <Set name="timeBetweenEvictionRunsMillis">30000</Set>
                <Set name="numTestsPerEvictionRun">3</Set>
                <Set name="minEvictableIdleTimeMillis">60000</Set>
                <Set name="defaultAutoCommit">true</Set>
            </New>
        </Arg>
    </New>
    <New id="defaultDs" class="org.eclipse.jetty.plus.jndi.Resource">
        <Arg></Arg>
        <Arg>jdbc/harvest_read</Arg>
        <Arg>
            <New class="org.apache.commons.dbcp.BasicDataSource">
                <Set name="driverClassName">org.postgresql.Driver</Set>
                <Set name="url">jdbc:postgresql://db-2-aws-syd.aodn.org.au:5432/harvest?loginTimeout=1000&amp;ssl=true&amp;sslfactory=org.postgresql.ssl.NonValidatingFactory</Set>
                <Set name="username">abhasin</Set>
                <Set name="password">******</Set>
                <Set name="validationQuery">SELECT 1</Set>
                <Set name="maxActive">3</Set>
                <Set name="maxIdle">2</Set>
                <Set name="maxWait">-1</Set>
                <Set name="testOnBorrow">true</Set>
                <Set name="testWhileIdle">true</Set>
                <Set name="testOnReturn">true</Set>
                <Set name="timeBetweenEvictionRunsMillis">30000</Set>
                <Set name="numTestsPerEvictionRun">3</Set>
                <Set name="minEvictableIdleTimeMillis">60000</Set>
                <Set name="defaultAutoCommit">true</Set>
            </New>
        </Arg>
    </New>
</Configure>
```

## override-web.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">
<web-app>
    <context-param>
        <param-name>GEOSERVER_DATA_DIR</param-name>
        <!--<param-value>src/jetty/geoserver_data</param-value>-->
        <param-value>/home/ankit/AODN/geoserver-config</param-value>
    </context-param>
  
    <context-param>
        <param-name>liquibase.changelog</param-name>
        <param-value>changelog.xml</param-value>
    </context-param>
    
    <context-param>
        <param-name>liquibase.datasource</param-name>
        <param-value>java:comp/env/jdbc/default</param-value>
    </context-param>
    
    <context-param>
        <param-name>liquibase.onerror.fail</param-name>
        <param-value>true</param-value>
    </context-param>
    
<!--    <listener>
        <listener-class>liquibase.integration.servlet.LiquibaseServletListener</listener-class>
    </listener>-->

<!--    <resource-ref>
       <description>Default jndi data source</description>
       <res-ref-name>jdbc/default</res-ref-name>
       <res-type>javax.sql.DataSource</res-type>
       <res-auth>Container</res-auth>
    </resource-ref>-->
    <resource-ref>
        <res-ref-name>jdbc/harvest_read</res-ref-name>
        <res-type>javax.sql.DataSource</res-type>
        <res-auth>Container</res-auth>
    </resource-ref>
</web-app>

```
## Add user to geoserver-config/security/usergroup/default/users.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<userRegistry version="1.0" xmlns="http://www.geoserver.org/security/users" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.geoserver.org/security/users users.xsd ">
  <users>
	<user enabled="true" name="admin" password="plain:admin"/>
  </users>  
  <groups>
  </groups>  
</userRegistry>
```
