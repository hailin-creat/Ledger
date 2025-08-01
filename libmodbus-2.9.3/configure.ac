# libmodbus package version number, (as distinct from shared library version)
# An odd micro number indicates in-progress development from Git
# An even micro number indicates a released version
#
# Making a point release:
# - increase libmodbus_version_micro to the next even number
#
# After the release:
# - increase libmodbus_version_minor to the next odd number
#
# Take care to update the libtool versioning when required (LIBMODBUS_LD_*).
# http://www.gnu.org/software/libtool/manual/html_node/Libtool-versioning.html
#
m4_define([libmodbus_version_major], [2])
m4_define([libmodbus_version_minor], [9])
m4_define([libmodbus_version_micro], [3])

m4_define([libmodbus_release_status],
            [m4_if(m4_eval(libmodbus_version_minor % 2), [1], [snapshot],
               [release])])

m4_define([libmodbus_version],
        [libmodbus_version_major.libmodbus_version_minor.libmodbus_version_micro])

AC_PREREQ(2.63)
AC_INIT([libmodbus],[libmodbus_version],[stephane.raimbault@gmail.com])
AC_CONFIG_SRCDIR([src/modbus.c])
AC_CONFIG_HEADERS([config.h])
AM_INIT_AUTOMAKE([foreign])
# enable nice build output on automake1.11
m4_ifdef([AM_SILENT_RULES],[AM_SILENT_RULES([yes])])

LIBMODBUS_VERSION_MAJOR=libmodbus_version_major
LIBMODBUS_VERSION_MINOR=libmodbus_version_minor
LIBMODBUS_VERSION_MICRO=libmodbus_version_micro
LIBMODBUS_VERSION=libmodbus_version
AC_SUBST(LIBMODBUS_VERSION_MAJOR)
AC_SUBST(LIBMODBUS_VERSION_MINOR)
AC_SUBST(LIBMODBUS_VERSION_MICRO)
AC_SUBST(LIBMODBUS_VERSION)

# ABI version
LIBMODBUS_LD_CURRENT=4
LIBMODBUS_LD_REVISION=0
LIBMODBUS_LD_AGE=0
LIBMODBUS_LT_VERSION_INFO=$LIBMODBUS_LD_CURRENT:$LIBMODBUS_LD_REVISION:$LIBMODBUS_LD_AGE
AC_SUBST(LIBMODBUS_LT_VERSION_INFO)

AC_CANONICAL_HOST

# Check whether we are building for Win32
os_win32="false"
case "${host_os}" in
      *mingw32*)
		os_win32="true"
	;;
esac
AM_CONDITIONAL(OS_WIN32, test "$os_win32" = "true")

# Checks for programs.
AC_PROG_CC
AC_PROG_CXX
AC_DISABLE_STATIC
AC_LIBTOOL_WIN32_DLL
AC_PROG_LIBTOOL
AC_PROG_MAKE_SET
AC_PROG_LIBTOOL
AC_CHECK_HEADERS([ \
    termios.h \
    sys/time.h \
    unistd.h \
    errno.h \
    limits.h \
    fcntl.h \
    sys/types.h \
    sys/socket.h \
    sys/ioctl.h \
    netinet/in.h \
    netinet/tcp.h \
    arpa/inet.h \
])

# Checks for header files.
AC_HEADER_STDC

# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_SIZE_T
AC_HEADER_TIME
# AC_TYPE_UINT*_T: not supported by autoconf-2.59 of CentOS 5.3
# AC_TYPE_UINT16_T
# AC_TYPE_UINT32_T
# AC_TYPE_UINT8_T

# Cygwin defines IPTOS_LOWDELAY but can't handle that flag so it's necessary to
# workaround that problem and Cygwin doesn't define MSG_DONTWAIT.
AC_CHECK_DECLS([__CYGWIN__])

# Checks for library functions.
AC_FUNC_FORK
AC_CHECK_FUNCS([gettimeofday inet_ntoa memset select socket strerror strlcpy])

# Add -Wall -Werror for GCC if not already there
if test "x$GCC" = "xyes"; then
  case " $CFLAGS " in
  *[[\ \	]]-Wall[[\ \	]]*) ;;
  *) CFLAGS="$CFLAGS -Wall" ;;
  esac
fi

if test "x$GCC" = "xyes"; then
  case " $CFLAGS " in
  *[[\ \	]]-Werror[[\ \	]]*) ;;
  *) CFLAGS="$CFLAGS -Werror" ;;
  esac
fi

AC_CONFIG_FILES([
        Makefile
        src/Makefile
        src/modbus-version.h
        tests/Makefile
        libmodbus.pc
])
AC_OUTPUT
