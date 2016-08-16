## Get all sequences
```
SELECT c.relname FROM pg_class c WHERE c.relkind = 'S';
```

## SQL to get current hibernate_sequence
```
select currval('hibernate_sequence');
```

## SQL to get next hibernate_sequence
```
select nextval('hibernate_sequence');
```
