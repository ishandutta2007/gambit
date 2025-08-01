dnl
dnl This file is part of Gambit
dnl Copyright (c) 1994-2025, The Gambit Project (https://www.gambit-project.org)
dnl
dnl FILE: configure.ac
dnl autoconf configuration script for Gambit
dnl
dnl This program is free software; you can redistribute it and/or modify
dnl it under the terms of the GNU General Public License as published by
dnl the Free Software Foundation; either version 2 of the License, or
dnl (at your option) any later version.
dnl
dnl This program is distributed in the hope that it will be useful,
dnl but WITHOUT ANY WARRANTY; without even the implied warranty of
dnl MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
dnl GNU General Public License for more details.
dnl
dnl You should have received a copy of the GNU General Public License
dnl along with this program; if not, write to the Free Software
dnl Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA 02111-1307, USA.
dnl

AC_INIT([gambit],[16.3.0])
AC_CONFIG_SRCDIR([src/gambit.h])
AM_INIT_AUTOMAKE([subdir-objects foreign])
dnl AC_CONFIG_MACRO_DIR([m4])
AM_OPTIONS_WXCONFIG

dnl User-specified options
dnl By default, we build the GUI
AC_ARG_ENABLE(gui,
[  --disable-gui           don't build graphical interface ],
[ case "${enableval}" in
  yes) with_gui=true ;;
  no)  with_gui=false ;;
  *)  AC_MSG_ERROR(bad value ${enableval} for --enable-gui) ;;
 esac], [with_gui=true])
AM_CONDITIONAL(WITH_GUI, test x$with_gui = xtrue)

AC_DEFUN([MINGW_AC_WIN32_NATIVE_HOST],
[AC_CACHE_CHECK([whether we are building for a Win32 host],
                [mingw_cv_win32_host],
AC_COMPILE_IFELSE([AC_LANG_SOURCE([
#ifdef _WIN32
choke me
#endif
])], [mingw_cv_win32_host=no], [mingw_cv_win32_host=yes]))])

dnl Checks for programs.
AC_PROG_CC
AC_PROG_CXX
dnl LT_INIT
AM_PROG_CC_C_O
MINGW_AC_WIN32_NATIVE_HOST
AM_CONDITIONAL(IS_WIN32, [test x$mingw_cv_win32_host = xyes])

dnl Check for C++17 support
AX_CXX_COMPILE_STDCXX(17)

if test x$with_gui = xtrue; then
  dnl------------------------
  dnl Checking for wxWidgets
  dnl------------------------

  dnl check for wx-config
  AM_PATH_WXCONFIG(3.2.0, WXCONFIG=1)
  if test "$WXCONFIG" != 1; then
      AC_MSG_WARN([
            Did not find a new enough version of wxWidgets; disabling build of
            graphical interface.  If you want to build the graphical interface,
            please check that wx-config is in path, the directory
            where wxWidgets libraries are installed (returned by
            'wx-config --libs' command) is in LD_LIBRARY_PATH or
            equivalent variable and wxWidgets is version 3.2.0 or above.
      ])
      AM_CONDITIONAL(WITH_GUI, test x0 = x1)
  fi
fi

if test x$with_gui = xtrue; then
  dnl-----------------------------
  dnl Checking for wxWidgets port
  dnl-----------------------------

  AC_MSG_CHECKING(for wxWidgets port)
  if test `echo ${WX_CXXFLAGS} | grep -c __WXX11__` -eq 1; then
    AC_MSG_RESULT(universal)
    AC_MSG_WARN([
            Found universal port of wxWidgets; disabling build of graphical
            interface.  The graphical interface cannot be built with this
            port because this port does not implement drag-and-drop capability.
            Gambit recommends the GTK port of wxWidgets for best results.
    ])
    AM_CONDITIONAL(WITH_GUI, test x0 = x1)
  fi

  if test `echo ${WX_CXXFLAGS} | grep -c __WXMOTIF__` -eq 1; then
    AC_MSG_RESULT(Motif)
    AC_MSG_WARN([
            Found Motif port of wxWidgets; disabling build of graphical
            interface.  The graphical interface cannot be built with this
            port because this port does not implement drag-and-drop capability.
            Gambit recommends the GTK port of wxWidgets for best results.
    ])
    AM_CONDITIONAL(WITH_GUI, test x0 = x1)
  fi

  if test `echo ${WX_CXXFLAGS} | grep -c __WXGTK__` -eq 1; then
    AC_MSG_RESULT(GTK)
    WXPORT="GTK"
  fi

  if test `echo ${WX_CXXFLAGS} | grep -c __WXMSW__` -eq 1; then
    AC_MSG_RESULT(Windows)
    WXPORT="MSW"
    dnl Windows uses the resources file
    dnl RC_OBJECT="gambitrc.o"
    dnl RC_OBJECT_PATH="bitmaps/gambitrc.o"
    dnl AC_SUBST(RC_OBJECT)
    dnl AC_SUBST(RC_OBJECT_PATH)
    dnl We insist on static build on Windows, not depending on
    dnl a shared wxWidgets library.
    WX_CXXFLAGS=`echo $WX_CXXFLAGS | sed 's/-DWXUSINGDLL//g'`
  fi

  if test `echo ${WX_CXXFLAGS} | grep -c __WXMAC__` -eq 1; then
    AC_MSG_RESULT(Macintosh)
    WXPORT="MAC"
  fi

  if test -z "$WXPORT"; then
    AC_MSG_WARN([
            Could not identify wxWidgets port.  Disabling build
            of Gambit graphical interface.
    ])
    AM_CONDITIONAL(WITH_GUI, test x0 = x1)
  fi
fi

AC_SUBST(WX_LIBS)
AC_SUBST(WX_LIBS_STATIC)
AC_SUBST(WX_CXXFLAGS)


REZFLAGS=`echo $CXXFLAGS $WX_CXXFLAGS | sed 's/-mthreads//g' | sed 's/-g//g' | sed 's/-O. / /g' | sed 's/-I/--include-dir /g'`
AC_SUBST(REZFLAGS)

AC_CONFIG_FILES([Makefile])
AC_OUTPUT
