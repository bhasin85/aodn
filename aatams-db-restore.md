## Database Restore
```
sudo -u postgres time pg_restore -d aatams -v --no-owner aatams.dump
```

## Backup Database : Optional
Create a backup aatams database for fast restore
```
CREATE DATABASE aatams_backup WITH TEMPLATE aatams;
```

## Drop existing connections : Optional
```
SELECT pg_terminate_backend(pg_stat_activity.pid)
FROM pg_stat_activity
WHERE pg_stat_activity.datname = 'aatams'
  AND pid <> pg_backend_pid();
```  

## Restore Database : Optional
Restore a backup aatams database for fast restore
```
DROP DATABASE aatams;
CREATE DATABASE aatams WITH TEMPLATE aatams_backup;
```

## Add Extensions
```
create extension postgis;
create extension pgcrypto;
```

## Update search path
```
alter database aatams set search_path to 'aatams','public';
```

## Create admin user
```
insert into sec_user(id, version, password_hash, username, class, status) 
          values (
                    99999999,
                    0,
                    (select encode(digest('aatams', 'sha256'), 'hex')),
                    'aatams',
                    'au.org.emii.aatams.Person',
                    null
                  ); 
```          
## Update admin user role
```
INSERT INTO sec_user_roles(sec_role_id, sec_user_id)
VALUES (5, 99999999);
```    
