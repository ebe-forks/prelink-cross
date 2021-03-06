dnl Process this file with autoconf to produce a configure script. -*-m4-*-
AC_INIT([prelink], [1.0])
AC_CONFIG_HEADERS(config.h)
AC_CONFIG_MACRO_DIR([m4])
AC_PREREQ(2.50)			dnl Minimum Autoconf version required.
AC_CANONICAL_SYSTEM
AC_ARG_PROGRAM

AM_INIT_AUTOMAKE([subdir-objects gnu 1.4])

ALL_LINGUAS=

AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_CXX
AC_PROG_CPP
AC_PROG_GCC_TRADITIONAL
AM_PROG_LIBTOOL

AC_ARG_ENABLE(64-bit,
[  --disable-64-bit        only support 32-bit ELF],
[case "${enableval}" in
  yes)  want64=true  ;;
  no)   want64=false ;;
  *)    AC_MSG_ERROR(bad value ${enableval} for 64-bit option) ;;
esac],[want64=true])dnl

AC_CHECK_HEADER(libelf.h,,[
  if test -f /usr/include/elfutils/libelf.h; then
    CPPFLAGS="$CPPFLAGS -I /usr/include/elfutils"
  elif test -f /usr/local/include/elfutils/libelf.h; then
    CPPFLAGS="$CPPFLAGS -I /usr/local/include/elfutils"
  elif test -f /usr/include/libelf/libelf.h; then
    CPPFLAGS="$CPPFLAGS -I /usr/include/libelf"
  elif test -f /usr/local/include/libelf/libelf.h; then
    CPPFLAGS="$CPPFLAGS -I /usr/local/include/libelf"
  else
    AC_MSG_ERROR(libelf.h not found)
  fi])
LIBGELF=""
if test "$want64"x = falsex; then
  GELFINCLUDE='-I$(top_srcdir)/gelfx32'
else
  GELFINCLUDE='-I$(top_srcdir)/gelfx'
  AC_CHECK_HEADER(gelf.h,,[
    GELFINCLUDE="$GELFINCLUDE "'-I$(top_srcdir)/gelf'
    LIBGELF='../gelf/libgelf.la'
  ])
fi
AC_CHECK_LIB(elf,elf_begin)
AC_SUBST(GELFINCLUDE)
AC_SUBST(LIBGELF)

dnl Now check what kind of libelf we will link against
AC_CHECK_FUNC(gelf_getvernaux,[newbu=true],[newbu=false])

dnl Check if FTW_ACTIONRETVAL is supported
AC_CACHE_CHECK([whether FTW_ACTIONRETVAL is supported],
	       [ac_cv_ftw_actionretval], [dnl
  AC_EGREP_CPP([FTW supports ACTIONRETVAL], [
  #ifndef _GNU_SOURCE
  #define _GNU_SOURCE 1
  #endif
  #include <ftw.h>
  #ifdef FTW_ACTIONRETVAL
  FTW supports ACTIONRETVAL
  #endif
  ], ac_cv_ftw_actionretval=yes, ac_cv_ftw_actionretval=no)])
if test "x$ac_cv_ftw_actionretval" = xyes; then
  AC_DEFINE(HAVE_FTW_ACTIONRETVAL, 1,
	    [Define if FTW_ACTIONRETVAL is supported.])
fi

dnl SELinux checks
AC_ARG_ENABLE(selinux,
	      AS_HELP_STRING([--disable-selinux],
	      [Disable selinux support in the prelinker]),
	      [ enable_selinux="$enableval" ],
	      [ enable_selinux="yes" ])

if test "x$enable_selinux" != "xyes"; then
  ac_cv_lib_selinux_is_selinux_enabled=no
  ac_cv_header_selinux_selinux_h=no
fi

AC_CHECK_LIB(selinux,is_selinux_enabled)
AC_CHECK_HEADERS(selinux/selinux.h)

dnl This test must come as early as possible after the compiler configuration
dnl tests, because the choice of the file model can (in principle) affect
dnl whether functions and headers are available, whether they work, etc.
if test x"$newbu" = xtrue; then
  # Don't use LFS for libelf-0.x
  AC_SYS_LARGEFILE
fi

AC_LIBELF_SXWORD

dnl Elf64_Byte is specific to the 64-bit MIPS psABI and may not be defined.
AC_CHECK_TYPES([Elf64_Byte], [], [], [#include <libelf.h>])

# Package version.
AC_ARG_WITH(pkgversion,
  AS_HELP_STRING([--with-pkgversion=PKG],
                 [Add PKG to the version string]),
  [case "$withval" in
    yes) AC_MSG_ERROR([package version not specified]) ;;
    no)  PKGVERSION= ;;
    *)   PKGVERSION=" ($withval)" ;;
   esac],
  PKGVERSION=
)
AC_SUBST(PKGVERSION)

# The location to which bugs should be reported.
AC_ARG_WITH(bugurl,
  AS_HELP_STRING([--with-bugurl=URL],
                 [Direct users to URL to report a bug]),
  [case "$withval" in
    yes) AC_MSG_ERROR([bug URL not specified]) ;;
    no)  REPORT_BUGS_TO="" ;;
    *)   REPORT_BUGS_TO="<URL:$withval>" ;;
   esac],
   REPORT_BUGS_TO="<mark.hatle@windriver.com>"
)
AC_SUBST(REPORT_BUGS_TO)

AC_OUTPUT([Makefile
	   src/Makefile
	   gelfx/Makefile
	   gelfx32/Makefile
	   gelf/Makefile
	   m4/Makefile
	   patches/Makefile
	   doc/Makefile
	   testsuite/Makefile])
