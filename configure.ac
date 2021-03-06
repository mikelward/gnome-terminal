m4_define([gt_version_major],[2])
m4_define([gt_version_minor],[31])
m4_define([gt_version_micro],[2])
m4_define([gt_version_extra],[-git])
m4_define([gt_version],[gt_version_major().gt_version_minor().gt_version_micro()gt_version_extra])

m4_define([gt_api_version],[0])

AC_INIT([GNOME Terminal],[gt_version],[http://bugzilla.gnome.org/enter_bug.cgi?product=gnome-terminal],[gnome-terminal])
AC_CONFIG_SRCDIR([src/terminal.c])
AC_CONFIG_HEADERS([config.h])

AM_INIT_AUTOMAKE([1.9 foreign no-dist-gzip dist-bzip2])

m4_ifdef([AM_SILENT_RULES],[AM_SILENT_RULES([no])])

GETTEXT_PACKAGE=gnome-terminal
AC_SUBST([GETTEXT_PACKAGE])
AC_DEFINE_UNQUOTED([GETTEXT_PACKAGE],["$GETTEXT_PACKAGE"],[Gettext package])

AC_C_BIGENDIAN

if test -z "$enable_maintainer_mode"; then
  enable_maintainer_mode=yes
fi
AM_MAINTAINER_MODE([enable])

PKG_PROG_PKG_CONFIG
AC_PROG_CC
AC_ISC_POSIX
AC_HEADER_STDC
AM_PROG_LIBTOOL
IT_PROG_INTLTOOL([0.40.0])

GNOME_COMMON_INIT
GNOME_DEBUG_CHECK
GNOME_COMPILE_WARNINGS([maximum])
#GNOME_MAINTAINER_MODE_DEFINES

AM_GLIB_GNU_GETTEXT

GLIB_REQUIRED=2.16.0
GTK_REQUIRED=2.14.0
GCONF_REQUIRED=2.14.0
DBUS_GLIB_REQUIRED=0.6
VTE_REQUIRED=0.25.1

GDK_TARGET="$($PKG_CONFIG --variable target gdk-2.0)"

case "$GDK_TARGET" in
  x11) PLATFORM_DEPS="x11" ;;
  *) PLATFORM_DEPS="" ;;
esac

PKG_CHECK_MODULES([TERM],
  [vte >= $VTE_REQUIRED
   glib-2.0 >= $GLIB_REQUIRED
   gthread-2.0
   gio-2.0
   gtk+-2.0 >= $GTK_REQUIRED
   gconf-2.0 >= $GCONF_REQUIRED
   dbus-glib-1 >= $DBUS_GLIB_REQUIRED
   $PLATFORM_DEPS])

PKG_CHECK_EXISTS([gtk+-2.0 >= 2.18.0],[have_gtk_2_18=yes],[have_gtk_2_18=no])
AM_CONDITIONAL([HAVE_GTK_2_18],[test "$have_gtk_2_18" = "yes"])

# Check the smclient backend
SMCLIENT_PKGS=
AC_MSG_CHECKING([which smclient backend to use])
AC_ARG_WITH([smclient],
  [AS_HELP_STRING([--with-smclient-backend],[which smclient backend to use (xsmp/win32/quartz)])],
  [],
  [case "$GDK_TARGET" in
    x11) with_smclient=xsmp SMCLIENT_PKGS="sm" ;;
    win32|quartz) with_smclient=$GDK_TARGET ;;
    *) with_smclient=no ;;
   esac])
AC_MSG_RESULT([$with_smclient])

if test "$with_smclient" != "no"; then
  AC_DEFINE([WITH_SMCLIENT],[1],[Define if smclient is enabled])
fi

if test -n "$SMCLIENT_PKGS"; then
  PKG_CHECK_MODULES([SMCLIENT],[$SMCLIENT_PKGS])
  AC_SUBST([SMCLIENT_CFLAGS])
  AC_SUBST([SMCLIENT_LIBS])
fi

AM_CONDITIONAL([WITH_SMCLIENT],[test "$with_smclient" != "no"])
AM_CONDITIONAL([WITH_SMCLIENT_XSMP],[test "$with_smclient" = "xsmp"])
AM_CONDITIONAL([WITH_SMCLIENT_WIN32],[test "$with_smclient" = "win32"])
AM_CONDITIONAL([WITH_SMCLIENT_QUARTZ],[test "$with_smclient" = "quartz"])

# GConf
AC_PATH_PROG(GCONFTOOL, gconftool-2, no)

if test x"$GCONFTOOL" = xno; then
  AC_MSG_ERROR([gconftool-2 executable not found in your path - should be installed with GConf])
fi

AM_GCONF_SOURCE_2

GLIB_GENMARSHAL="$($PKG_CONFIG --variable=glib_genmarshal glib-2.0)"
AC_SUBST([GLIB_GENMARSHAL])
GLIB_MKENUMS="$($PKG_CONFIG --variable=glib_mkenums glib-2.0)"
AC_SUBST([GLIB_MKENUMS])

AC_PATH_PROG([GTK_BUILDER_CONVERT],[gtk-builder-convert],[false])
if test "$GTK_BUILDER_CONVERT" = "false"; then
  AC_MSG_ERROR([gtk-builder-convert not found])
fi

AC_PATH_PROG([DBUS_BINDING_TOOL],[dbus-binding-tool],[false])
if test "$DBUS_BINDING_TOOL" = "false"; then
  AC_MSG_ERROR([dbus-binding-tool not found])
fi

GNOME_DOC_INIT

# ****************************

AC_SUBST([TERMINAL_MAJOR_VERSION],[gt_version_major])
AC_SUBST([TERMINAL_MINOR_VERSION],[gt_version_minor])
AC_SUBST([TERMINAL_MICRO_VERSION],[gt_version_micro])
AC_SUBST([TERMINAL_API_VERSION],[gt_api_version])

# ****************************

AC_CONFIG_FILES([
Makefile
gnome-terminal.desktop.in
src/Makefile
src/terminal-version.h
src/skey/Makefile
help/Makefile
help/bg/Makefile
help/ro/Makefile
help/ja/Makefile
help/zh_TW/Makefile
po/Makefile.in
])
AC_OUTPUT

# ****************************

echo "
gnome-terminal-$VERSION:

	prefix:                 ${prefix}
	source code location:	${srcdir}
	compiler:		${CC}

        using smclient:         ${with_smclient}
"
