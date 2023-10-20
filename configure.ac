# Autoconf configuration for Savane.
#
# Copyright (C) 1999, 2000 The SourceForge Crew
# Copyright (C) 2000-2006 Mathieu Roy
# Copyright (C) 2014, 2016, 2017 Assaf Gordon
# Copyright (C) 2001-2011, 2013, 2017 Sylvain Beucler
# Copyright (C) 2013, 2014, 2017-2023 Ineiev
#
# This file is part of Savane.
#
# Code written before 2008-03-30 (commit 8b757b2565ff) is distributed
# under the terms of the GNU General Public license version 3 or (at your
# option) any later version; further contributions are covered by
# the GNU Affero General Public license version 3 or (at your option)
# any later version.  The license notices for the AGPL and the GPL follow.
#
# Savane is free software: you can redistribute it and/or modify
# it under the terms of the GNU Affero General Public License as
# published by the Free Software Foundation, either version 3 of the
# License, or (at your option) any later version.
#
# Savane is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU Affero General Public License for more details.
#
# You should have received a copy of the GNU Affero General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#
# Savane is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as
# published by the Free Software Foundation, either version 3 of the
# License, or (at your option) any later version.
#
# Savane is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

AC_INIT([Savane], [3.12], [savannah-hackers-public@gnu.org], [savane],
  [https://savannah.gnu.org/projects/administration])

AC_CONFIG_AUX_DIR(autotools)
# For future version of aclocal:
AC_CONFIG_MACRO_DIR(autotools/m4)
AM_INIT_AUTOMAKE([1.11.1])
AM_PO_SUBDIRS
AC_PROG_INSTALL
AC_PROG_LN_S

AC_PATH_PROG(PERL, [perl])
AS_CASE(["x$PERL"], [x], [AC_MSG_ERROR([Perl not found])])

AC_PATH_PROG(PHP, [php])

AC_PATH_PROG(SV_DB_CMD, [mysql])
AS_CASE(["x$SV_DB_CMD"], [x], [AC_MSG_ERROR([MySQL client not found])])

# Look for Apache2 and MySQL server, needed for local testing.
AC_PATH_PROG([APACHE2_SERVER_BIN], [apache2], [],
  [$PATH$PATH_SEPARATOR/usr/sbin$PATH_SEPARATOR/usr/local/sbin/])
AC_SUBST([APACHE2_SERVER_BIN])
AC_PATH_PROG([MYSQLD_SAFE_BIN], [mysqld_safe], [],
  [$PATH$PATH_SEPARATOR/usr/sbin$PATH_SEPARATOR/usr/local/sbin/])
AC_SUBST([MYSQLD_SAFE_BIN])
AC_PATH_PROG([MYSQL_INSTALLDB_BIN], [mysql_install_db], [],
  [$PATH$PATH_SEPARATOR/usr/sbin$PATH_SEPARATOR/usr/local/sbin/])
AC_SUBST([MYSQL_INSTALLDB_BIN])
AC_PATH_PROG([MYSQLADMIN_BIN], [mysqladmin], [],
  [$PATH$PATH_SEPARATOR/usr/sbin$PATH_SEPARATOR/usr/local/sbin/])
AC_SUBST([MYSQLADMIN_BIN])
AC_PATH_PROG([MYSQL_BIN], [mysql], [],
  [$PATH$PATH_SEPARATOR/usr/sbin$PATH_SEPARATOR/usr/local/sbin/])
AC_SUBST([MYSQL_BIN])
AC_PATH_PROG(GIT, [git])

AC_MSG_CHECKING([whether to regenerate ChangeLog])
AC_ARG_ENABLE([changelog],
  [AS_HELP_STRING([--enable-changelog], [update ChangeLog from git logs])],
  [], [enable_changelog=no])
AC_MSG_RESULT([$enable_changelog])

AS_CASE(["x$GIT"], [x], [], [
  AS_IF([test -d "$srcdir/.git"], [
    AS_CASE(["x$enable_changelog"], [xyes], [
      AS_ECHO(["Updating ChangeLog..."])
      sed '1,/^=\{51\}/{/^=\{51\}/p;d}' "$srcdir/ChangeLog" > ChangeLog.git
      "$GIT" --git-dir="$srcdir/.git" log --no-decorate --numstat \
        | sed "s,[[[:space:]]]*$,," > "$srcdir/ChangeLog"
      echo >> "$srcdir/ChangeLog"
      cat ChangeLog.git >> "$srcdir/ChangeLog"
      rm ChangeLog.git
    ])
  ])
])

AC_MSG_CHECKING([whether to install frontend part])
AC_ARG_ENABLE([frontend],
  [AS_HELP_STRING([--disable-frontend], [skip frontend (PHP) part])],
  [], [enable_frontend=yes])
AS_CASE(["x$enable_frontend"], [xno], [], [enable_frontend=yes])
AC_MSG_RESULT([$enable_frontend])
AS_CASE(["$enable_frontend.$PHP"], [yes.], [AC_MSG_ERROR([PHP not found])])
AM_CONDITIONAL([HAVE_FRONTEND], [test "x$enable_frontend" = xyes])

AC_MSG_CHECKING([for Git commit])
SV_GIT_COMMIT=`sed 's,^commit ,,;q' $srcdir/ChangeLog`
commit_source='guessed from ChangeLog'
AS_IF([test -d "$srcdir/.git"], [
  head=`sed 's,^ref: ,,;q' $srcdir/.git/HEAD`
  SV_GIT_COMMIT=`cat $srcdir/.git/$head`
  commit_source='extracted from Git repository'
])
AC_MSG_RESULT([$SV_GIT_COMMIT])
AS_IF([echo $SV_GIT_COMMIT | grep -q ['^[a-f0-9]\{40,\}$']],
  [],
  [AC_MSG_ERROR([Failed to identify Git commit of the source code.])
])
AC_SUBST([SV_GIT_COMMIT])

AC_MSG_CHECKING([for database name])
AC_ARG_WITH([db-name],
  AS_HELP_STRING([--with-db-name], [name of the database [savane]]),
  SV_DB_NAME="$withval", SV_DB_NAME="savane")
AC_MSG_RESULT([$SV_DB_NAME])

AC_MSG_CHECKING([for database options])
AC_ARG_WITH([db-options],
  AS_HELP_STRING([--with-db-options],
    [parameters to give MySQL when initializing the database []]),
  SV_DB_OPTIONS="$withval")
AC_MSG_RESULT([$SV_DB_OPTIONS])

AC_SUBST([SV_DB_NAME])
AC_SUBST([SV_DB_OPTIONS])

SV_CONF="$sysconfdir/savane"
AC_SUBST(SV_CONF)

AC_MSG_CHECKING([for Apache user])
AC_ARG_WITH([apache-user],
  AS_HELP_STRING([--with-apache-user], [webserver user [current user]]),
  [WWW_SERVER_USER="$withval"], [WWW_SERVER_USER=`whoami`])
AC_MSG_RESULT([$WWW_SERVER_USER])

AC_MSG_CHECKING([for Apache group])
AC_ARG_WITH([apache-group],
  AS_HELP_STRING([--with-apache-group], [webserver group [the same as user]]),
  [WWW_SERVER_GROUP="$withval"], [WWW_SERVER_GROUP="$WWW_SERVER_USER"])
AC_MSG_RESULT([$WWW_SERVER_GROUP])

AC_CONFIG_FILES([
  Makefile autotools/m4/Makefile etc/Makefile frontend/Makefile
  frontend/php/images/Makefile lib/Makefile local/Makefile
  frontend/php/include/ac_config.php
])
AC_CONFIG_FILES([po/Makefile], [AM_POSTPROCESS_PO_MAKEFILE])
AC_OUTPUT

AS_ECHO([])
AS_ECHO(["Configured $PACKAGE_STRING"])
AS_ECHO(["Git commit: $SV_GIT_COMMIT ($commit_source)"])
AS_ECHO(["Configuration directory: $SV_CONF"])
AS_ECHO(["Apache user: $WWW_SERVER_USER"])
AS_ECHO(["Apache group: $WWW_SERVER_GROUP"])
AS_ECHO(["Database name: $SV_DB_NAME"])
AS_ECHO(["Install frontend (PHP) part: $enable_frontend"])
AS_ECHO(["Update ChangeLog from git: $enable_changelog"])
AS_CASE(["x$SV_DB_OPTIONS"], [x], [],
  [AS_ECHO(["Database options: $SV_DB_OPTIONS"])]
)

AS_ECHO([])
AS_ECHO(["Now you can run"])
AS_ECHO(["  make all install installcheck"])
AS_ECHO(["to install and check the package."])
