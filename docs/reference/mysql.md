# MySQL

This section gives some practical tips on working with MySQL.

## Working with MySQL dumps

### Creating a MySQL dump

    mysqldump -u root --single-transaction dbname > mydump.sql

Optionally exclude less important tables:

    mysqldump -u root myapplication > dump.sql --single-transaction --ignore-table=myapplication._SecurityContext --ignore-table=myapplication._RequestLogEntry


### Loading a small Mysql dump

    mysql -u root dbname < mydump.sql

### Loading a large MySQL dump

When loading large MySQL dumps (for local testing), convert them to MyISAM (lack of transactions makes it unusable for production db, but it loads a lot faster due to lack of foreign key checks).

Before loading the dump, increase these settings in /etc/my.cnf and restart MySQL to load the changed settings:

    key_buffer_size=1024m
    max_allowed_packet=1024m

    mysqladmin -u root shutdown
    mysqld -u root &

Then run

    cat mydump.sql | sed s/ENGINE=InnoDB/ENGINE=MyISAM/ | mysql -u root

Or, if you want to create an intermediate file with the MyISAM dump first (slower):

    sed s/ENGINE=InnoDB/ENGINE=MyISAM/ mydump.sql > mydump.sql.myisam
    mysql -u root dbname < mydump.sql.myisam


## MySQL Settings

Show status of InnoDB:

    mysql -u root 
    show innodb status;

See what MySQL is doing (e.g. expensive query):

    show processlist;

Check the current structure of a table, including foreign key constraints. This can be helpful in resolving issues caused by db mode 'update', which only adds columns but will not change an existing column:

    show create table _Alias;

We use the following settings for MySQL on our production server (NixOS/Linux):

    [mysqld]
    key_buffer_size = 256M
    max_allowed_packet = 64M
    sort_buffer_size = 2M
    read_buffer_size = 2M
    myisam_sort_buffer_size = 64M
    query_cache_size = 128M
    max_connections = 250

    [mysqldump]
    max_allowed_packet = 16M

    [isamchk]
    key_buffer = 256M
    sort_buffer_size = 256M

    [myisamchk]
    key_buffer = 256M
    sort_buffer_size = 256M

