mysql-m4
========

Autotools macro package for various MySQL related build tasks

Introduction
------------

This set of autoconf macros supports 

* building against the various MySQL APIs:
    * regular client library API
	* embedded server library API
	* User Defined Function (UDF) API
	* Plugin API
	* NDBAPI
* check for minimum required version
* check for a specifc fork (MySQL, MariaDB, ...)
* building against files from a server source dir


Example
-------

A minimal project using this may look like this:

    .
	|-- m4
    |   `-- ax_compare_version.m4
    |   `-- mysql.m4
	|-- configure.ac
	|-- example.c
    `-- Makefile.am

and could be built using

    autoreconf -vfi
	./configure
	make

The `mysql.m4` and `ax_compare_version.m4` files are
copies of the files in this directory, the other files
could look like this:

configure.ac:

    AC_CONFIG_MACRO_DIR([m4])
    AC_INIT([example], [0.0.1])
    
    WITH_MYSQL()
	MYSQL_USE_CLIENT_API()
	MYSQL_SUBST()
    
    AC_PROG_CC
    AC_CONFIG_FILES([Makefile])
	AC_OUTPUT
	
Makefile.m4:

	ACLOCAL_AMFLAGS = -I m4
    	
	AM_CFLAGS = @MYSQL_CFLAGS@ -Wall -Werror -Wno-unused-but-set-variable
    AM_LDFLAGS = @MYSQL_LDFLAGS@
    AM_LIBS = @MYSQL_LIBS@
    
	bin_PROGRAMS=example
	example_SOURCE=example.c

example.c:

	#include <stdio.h>
    #include <mysql.h>
    
    int main(int argc, char **argv)
    {
      printf("MySQL Client Version %lu\n", mysql_get_client_version());
    
      return EXIT_SUCCESS;
    }

Macros
------

The following variables are set after calling MYSQL_SUBST()

### WITH_MYSQL

Adds a `--with-mysql=...` option that expects either the path to the `mysql_config` 
(or `mariadb_config`) binary or the mysql install prefix. If no path is given it
checks for a `(mysql|maria)_config` binary in the current $PATH

This macro needs to be called before any `MYSQL_USE_*` and `MYSQL_NEED_*` macros

### WITH_MYSQL_SRC

Adds a `--with-mysql-src=...` option that expects the path to a mysql server
source directory. This option can be used instead or in addition to `WITH_MYSQL`,
but only one of `--with-mysql-src` or `--with-mysql` can be used when running
`configure`.

The idea behind this option is that some projects, especially plugins and UDFs,
may rely on header (or even source) files that come with the server source but
are not part of the installed headers.

This macro needs to be called before any `MYSQL_USE_*` and `MYSQL_NEED_*` macros

### MYSQL_USE_CLIENT_API

Sets flags necessary to compile and link against the mysql client library 
(or the mariadb native client library)

### MYSQL_USE_EMBEDDED_API

Sets flags necessary to compile and link against the mysql embedded server library

### MYSQL_USE_NDB_API

Sets flags necessary to compile and link against the MySQL Cluster NDBAPI

### MYSQL_USE_UDF_API

Sets flags necessary to build User Defined Function libraries

### MYSQL_USE_PLUGIN_API

Sets flags necessary to build mysql plugins

### MYSQL_NEED_VERSION

Check for a specific minimal mysql version.

    MYSQL_NEED_VERSION([5.5.0])

### MYSQL_NEED_FORK

Check for a specific mysql fork. Currently supported values are `mysql` and
`mariadb`. Other forks like `parcona` or `webscalesql` can't be detected yet.

    MYSQL_NEED_FORK([mariadb])

### MYSQL_SUBST

This needs to be the very last macro call, it collects results of all
previous macros and exports them as variables that can be using in 
`configure.ac` and `Makefile.am` files.

Variables
---------

### MYSQL_VERSION

Full version number of the detected mysql version

### MYSQL_FORK

Name of the detected mysql fork (only `mysql` or `mariadb` for now)

### MYSQL_CFLAGS

Compiler flags needed to compile C source files

### MYSQL_CXXFLAGS

Compiler flags needed to compile C++ source files

### MYSQL_LDFLAGS

Linker flags needed to link against the selected APIs

### MYSQL_LIBS

Libraries needed to link

### MYSQL_PLUGIN_DIR

Plugin library directory

### MYSQL_SRCDIR

Server source directory (when using `--with-mysql-src`)

