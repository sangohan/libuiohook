# JNativeHook: Global keyboard and mouse hooking for Java.
# Copyright (C) 2006-2013 Alexander Barker.  All Rights Received.
# http://code.google.com/p/jnativehook/

# JNativeHook is free software: you can redistribute it and/or modify
# it under the terms of the GNU Lesser General Public License as published
# by the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# JNativeHook is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#

# Define a few constants using m4 macros to prevent multiple definitions.
m4_define([NATIVEHOOK_MAJOR],	[1])
m4_define([NATIVEHOOK_MINOR],	[2])
m4_define([NATIVEHOOK_PATCH],	[0])
m4_define([NATIVEHOOK_RC],		[_M1])
m4_define([NATIVEHOOK_BUGS],	[alex@1stleg.com])
m4_define([NATIVEHOOK_VERSION_STRING], NATIVEHOOK_MAJOR[.]NATIVEHOOK_MINOR[.]NATIVEHOOK_PATCH[]NATIVEHOOK_RC)

m4_pattern_allow([AM_PROG_AR])

# Library versioning
# These numbers should be tweaked on every release. Read carefully:
# http://www.gnu.org/software/libtool/manual/html_node/Updating-version-info.html
# http://sourceware.org/autobook/autobook/autobook_91.html
lt_current="2"
lt_revision="4"
lt_age="1"
LTLDFLAGS="-version-info ${lt_current}:${lt_revision}:${lt_age}"


# Initialize autoconf.
AC_INIT([nativehook], [NATIVEHOOK_VERSION_STRING], [NATIVEHOOK_BUGS])

AC_PREREQ(2.64)
#AC_CANONICAL_SYSTEM requires src/native/install.sh
AC_CONFIG_SRCDIR([include/nativehook.h])
AC_CONFIG_FILES([
	pc/nativehook.pc \
	Makefile
])
AC_CONFIG_AUX_DIR([config])
AC_CONFIG_HEADERS([include/config.h:include/config.h.in])
AC_CONFIG_MACRO_DIR([m4])

# Initialize automake.
# FIXME This should use subdir-objects, but the makefile needs work.
AM_INIT_AUTOMAKE([1.9 foreign -Wall])

# Checks for language.
AC_LANG([C])

# Checks for programs.
AC_PROG_CC
AC_PROG_CC_C99
m4_ifdef([AM_PROG_AR], [AM_PROG_AR])

# Checks for header files
AC_C_CONST
AC_HEADER_STDBOOL
#AC_CHECK_HEADERS([inttypes.h stdint.h])

# Initialize libtool.
#LT_PREREQ([2.2])

# Check for opperating system.
AC_MSG_CHECKING(operating system)
AS_CASE([$host],
	[*-darwin*], [
		AC_MSG_RESULT([$host (Mac OS X Compatible)])
		backend="darwin"

		#LT_INIT
	],
	[*-freebsd* | *-linux* | *-solaris*], [
		AC_MSG_RESULT([$host (Unix Compatible)])
		backend="unix"

		#LT_INIT
	],
	[*-mingw* | *-cygwin* | *-msys* ], [
		AC_MSG_RESULT([$host (Windows Compatible)])
		backend="windows"

		#LT_INIT([dlopen pic-only win32-dll ])
		#LT_LANG([Windows Resource])
		AC_LIBTOOL_WIN32_DLL
	],
	[AC_MSG_ERROR([Unsupported operating system ${host}])])


AC_PROG_LIBTOOL

# Common Options
AC_ARG_ENABLE([debug],
	AS_HELP_STRING([--enable-debug], [Enable Debug Output (default: disabled)]),
	[enable_debug="$enableval"],
	[enable_debug="no"])

AC_ARG_ENABLE([demo],
	AS_HELP_STRING([--enable-demo], [Enable demo application (default: disabled)]),
	[enable_demo=$enableval],
	[enable_demo="no"])

# Unix Options
AC_ARG_ENABLE([xkb],
	AS_HELP_STRING([--disable-xkb], [Disable X Keyboard Extension (default: enabled)]),
	[enable_xkb="$enableval"],
	[enable_xkb="yes"])

AC_ARG_ENABLE([xt],
	AS_HELP_STRING([--disable-xt], [Disable X Toolkit Intrinsics (default: enabled)]),
	[enable_xt="$enableval"],
	[enable_xt="yes"])

AC_ARG_ENABLE([xf86misc],
	AS_HELP_STRING([--enable-xf86misc], [Enable XFree86-Misc X Extension (default: disabled)]),
	[enable_xf86misc="$enableval"],
	[enable_xf86misc="no"])

AC_ARG_ENABLE([xrecord-async],
	AS_HELP_STRING([--enable-xrecord-async], [Enable XRecord Asynchronous API (default: disabled)]),
	[enable_xrecord_async="$enableval"],
	[enable_xrecord_async="no"])

AC_ARG_ENABLE([xtest],
	AS_HELP_STRING([--disable-xtest], [Disable XTest Extension (default: enabled)]),
	[enable_xtest="$enableval"],
	[enable_xtest="yes"])

# Darwin Options
AC_ARG_ENABLE([corefoundation],
	AS_HELP_STRING([--disable-corefoundation],	[Disable CoreFoundation framework (default: enabled)]),
	[enable_corefoundation="$enableval"],
	[enable_corefoundation="yes"])

AC_ARG_ENABLE([iokit],
	AS_HELP_STRING([--disable-iokit],	[Disable IOKit framework (default: enabled)]),
	[enable_iokit="$enableval"],
	[enable_iokit="yes"])

AC_ARG_ENABLE([carbon-legacy],
	AS_HELP_STRING([--enable-carbon-legacy], [Enable legacy Carbon framework functionality (default: disabled)]),
	[enable_carbon_legacy="$enableval"],
	[enable_carbon_legacy="no"])


AS_IF([test "x$enable_debug" = "xyes"], [
	AC_DEFINE([USE_DEBUG], 1, [Enable Debug Output])
])

AS_IF([test "x$enable_demo" = "xyes"], [
	AM_PROG_CC_C_O
])

AS_IF(
	[test "x$backend" = "xunix"], [
		# Checks for libraries.
		PKG_PROG_PKG_CONFIG

		PKG_CHECK_MODULES([X11], [x11])
		LIBS="$X11_LIBS $LIBS"
		CFLAGS="$X11_CFLAGS $CFLAGS"
		REQUIRE="$REQUIRE x11"

		PKG_CHECK_MODULES([XTST], [xtst])
		LIBS="$XTST_LIBS $LIBS"
		CFLAGS="$XTST_CFLAGS $CFLAGS"
		REQUIRE="$REQUIRE xtst"

		# Check for XRecord.
		AC_CHECK_LIB([Xtst], [XRecordQueryVersion], [],
			[AC_MSG_ERROR([libXtst does not include XRecord extention!])],
			$LIBS)

		AC_CHECK_HEADERS([X11/extensions/record.h], [],
			[AC_MSG_ERROR([XRecord extention header could not be found!])],
			[#include <X11/Xlib.h>])

		AS_IF([test "x$enable_xkb" = "xyes"], [
			AC_DEFINE([USE_XKB], 1, [Enable X Keyboard Extension])
		])

		AS_IF([test "x$enable_xt" = "xyes"], [
			AC_DEFINE([USE_XT], 1, [Enable X Toolkit Intrinsics])
			PKG_CHECK_MODULES([XT], [xt])
			LIBS="$XT_LIBS $LIBS"
			CFLAGS="$XT_CFLAGS $CFLAGS"
			REQUIRE="$REQUIRE xt"
		])

		AS_IF([test "x$enable_xf86misc" = "xyes"], [
			AC_DEFINE([USE_XF86MISC], 1, [Enable XFree86-Misc X Extension])
			PKG_CHECK_MODULES([XF86MISC], [xxf86misc])
			LIBS="$XF86MISC_LIBS $LIBS"
			CFLAGS="$XF86MISC_CFLAGS $CFLAGS"
			REQUIRE="$REQUIRE xxf86misc"
		])

		AS_IF([test "x$enable_xrecord_async" = "xyes"], [
			AC_DEFINE([USE_XRECORD_ASYNC], 1, [Enable XRecord Asynchronous API])
			CFLAGS="$CFLAGS -D_POSIX_C_SOURCE=199309L"
		])

		AS_IF([test "x$enable_xtest" = "xyes"], [
			AC_CHECK_LIB([Xtst], [XTestQueryExtension],
				[AC_CHECK_HEADERS([X11/extensions/XTest.h],
						[AC_DEFINE([USE_XTEST], 1, [Enable XTest Extension])],
						[AC_MSG_WARN([XTest extention header could not be found!])],
						[#include <X11/Xlib.h>])],
				[AC_MSG_WARN([libXtst does not include XTest extention!])],
				$LIBS)
		])

		AX_PTHREAD([found_pthreads=yes], [found_pthreads=no])
		LIBS="$PTHREAD_LIBS $LIBS"
		CFLAGS="$PTHREAD_CFLAGS $CFLAGS"
	],
	[test "x$backend" = "xdarwin"], [
		if test "x$enable_corefoundation" = "xyes" ; then
			AC_DEFINE([USE_COREFOUNDATION], 1, [Enable CoreFoundation framework])
			LIBS="${LIBS} -framework ApplicationServices"
		fi

		if test "x$enable_iokit" = "xyes" ; then
			AC_DEFINE([USE_IOKIT], 1, [Enable IOKit framework])
			LIBS="${LIBS} -framework IOKit"
		fi

		if test "x$enable_carbon_legacy" = "xyes" ; then
			# FIXME AC_MSG_ERROR if this is a 64 bit target.
			AC_MSG_WARN([Carbon legacy functionality is required for OS X version 10.4 and should only be used on 32-bit targets.])
			AC_DEFINE([USE_CARBON_LEGACY], 1, [Enable Carbon framework legacy functionality])
		fi

		# Always add Carbon due to non-deprecated #define's
		LIBS="${LIBS} -framework Carbon"

		CFLAGS="$CFLAGS -I/usr/local/include"
		LDFLAGS="$LDFLAGS -L/usr/local/lib"
	],
	[test "x$backend" = "xwindows"], [
		AC_DEFINE([_WIN32_WINNT], 0x0500, [Set minimum Windows version to Windows 2000])

		AS_IF([test "x$enable_debug" = "xyes"], [
			CFLAGS="$CFLAGS -Wno-format"
		])

		#REQUIRE="$REQUIRE -lmsvcrt -luser32 -lkernel32"

		#EXTRA_LIBS="-lmsvcrt -luser32 -lkernel32"

		#LIBS="${LIBS} -lsetupapi"
	])

# OS info for Automake
AC_SUBST([backend])

# Requires for pkg-config
AC_SUBST([REQUIRE])
#AC_SUBST([EXTRA_LIBS])

# Should the demo application be built?
AM_CONDITIONAL([BUILD_DEMO], [test "x$enable_demo" = "xyes"])

# Department of redundancy department.  We have already checked the platform 3
# times!  I am sure there is a technical reason for this...
AM_CONDITIONAL(BUILD_DARWIN, test "x$backend" = "xdarwin")
AM_CONDITIONAL(BUILD_UNIX, test "x$backend" = "xunix")
AM_CONDITIONAL(BUILD_WINDOWS, test "x$backend" = "xwindows")

# Generate the "configure" script
AC_OUTPUT