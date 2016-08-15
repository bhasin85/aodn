Full stack traces
=================

To enable full stack traces just add the command line argument `-Dgrails.full.stacktrace=true` when calling `grails`, e. g.:

    grails -Dgrails.full.stacktrace=true run-app

SQL logging
===========

First you can enable SQL logging by adding the property `logSql = true` to the data source (in `DataSource.groovy`), e. g.:

    environments {
        development {
            dataSource {
                url = 'jdbc:mysql://localhost/dbname?autoreconnect=true'
                username = 'user'
                password = 'secretpwd'
                logSql = true
            }
        }
    }

This method has the disadvantage that the SQL commands are visible, though, the bound values are not.


Advanced SQL logging
====================

To produce more output, format the SQL commands, and see the bound parameter values, first turn off the `logSql` property for the database because otherwise it'll output SQL commands twice.

Then add the following properties in `DataSource.groovy`:

    hibernate {
        format_sql = true
        use_sql_comments = true
    }

This will produce far more readable SQL commands than simply `logSql` would do.  Then, add the following log4j settings to `Config.groovy`:

    log4j = {
        debug 'org.hibernate.SQL'
        trace 'org.hibernate.type'
    }

The first setting logs the SQL commands, the second one logs the bound parameters and the bindings of the result set.
