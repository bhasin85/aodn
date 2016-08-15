## Database Restore
```
sudo -u postgres time pg_restore -d aatams -v --no-owner aatams.dump
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
