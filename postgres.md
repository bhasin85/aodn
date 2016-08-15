## Get all sequences
```
SELECT c.relname FROM pg_class c WHERE c.relkind = 'S';
```

## SQL to get next hibernate_sequence
```
select nextval('hibernate_sequence');
```

