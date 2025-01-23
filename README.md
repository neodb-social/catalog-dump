neodb.social catalog data set
=============================

These data are exported from [neodb.social](https://neodb.social) (the flagship and largest NeoDB instance) via `pg_dump --data-only -t 'catalog_*'`, they are meant to be used in *fresh install* of NeoDB instances as initial data set only. 

note: 

- importing this data set is *completely optional* to run your own NeoDB instance, actually for most cases I recommend you don't use it, so that your own database is clean and managable. neodb.social also provides this data set thru a public API, so your own instance can, and by default will, query neodb.social when you search in your own NeoDB instance;
- this data set is crowdsourced by users in NeoDB network, data from other NeoDB instances may exist in this data set as well, there's no guarantee for the quality or accuracy of this data set;
- this data set is supposed to be public metadata information, however, we cannot 100% validate them given the volume; if you believe any piece of data violate your rights under US copyright laws, please contact us. 

how to use
----------

download data files from releases page

follow [the instructions on neodb.net](https://neodb.net/install/) to configure NeoDB, but DO NOT start the containers.

add `NEODB_IMAGE=neodb/neodb:0.11.4.1` in `.env` (change `0.11.4.1` to the version on the data set release page you download from) 

pull images, run migration, clean some tables before importing
```
docker compose --profile production pull
docker compose --profile production run --rm migration
echo 'DELETE FROM auth_permission; DELETE FROM django_content_type;' | docker compose --profile production run -T --rm shell neodb-manage dbshell
```

restore data set to your database
```
cat catalog_backup_20250122.sql.bz2.part* | bunzip2 -c | docker compose --profile production run -T --rm shell neodb-manage dbshell
```

remove `NEODB_IMAGE=neodb/neodb:0.11.4.1` from `.env`, [upgrade the instance](https://neodb.net/upgrade/)

clean it up
```
docker compose --profile production run shell neodb-manage catalog --purge
```

create search index
```
docker compose --profile production run shell neodb-manage index --reindex
```

now to test it, try search some popular title and they should show up in the results.
