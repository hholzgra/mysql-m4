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
	#include <stdio.h>

example.c:

    #include <mysql.h>
    
    int main(int argc, char **argv)
    {
      printf("MySQL Client Version %lu\n", mysql_get_client_version());
    
      return EXIT_SUCCESS;
    }

Macros
------

### WITH_MYSQL

### WITH_MYSQL_SRC

### MYSQL_USE_CLIENT_API

### MYSQL_USE_EMBEDDED_API

### MYSQL_USE_NDB_API

### MYSQL_USE_UDF_API

### MYSQL_USE_PLUGIN_API

### MYSQL_SUBST

### MYSQL_NEED_VERSION

### MYSQL_NEED_FORK



Variables
---------

### MYSQL_VERSION

### MYSQL_FORK

### MYSQL_CFLAGS

### MYSQL_CXXFLAGS

### MYSQL_LDFLAGS

### MYSQL_LIBS

### MYSQL_PLUGIN_DIR

### MYSQL_SRCDIR

