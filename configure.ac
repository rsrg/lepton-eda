dnl                                                      -*-Autoconf-*-
dnl Process this file with autoconf to generate configure.
dnl
dnl The AX_* macros are defined in files in the top-level m4
dnl directory.

#####################################################################
# Set up configuration system
#####################################################################

AC_INIT([Lepton EDA],
        [1.9.14],
        [https://github.com/lepton-eda/lepton-eda/issues],
        [lepton-eda],
        [https://github.com/lepton-eda/lepton-eda])
AC_PREREQ([2.60])

AC_CONFIG_SRCDIR([liblepton/src/liblepton.c])
AC_CONFIG_AUX_DIR([build-tools])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_HEADER([config.h])

AM_INIT_AUTOMAKE([parallel-tests color-tests foreign])
AM_SILENT_RULES([yes])

AC_GNU_SOURCE # FIXME for some reason this is needed?

#####################################################################
# Do version number magic
#####################################################################

AX_GIT_VERSION([20210407])

#####################################################################
# Windows/MinGW/Cygwin support
#####################################################################

AX_WINDOWS_FLAGS

#####################################################################
# Check for compiling & linking tools
#####################################################################

AC_PROG_CC
AC_PROG_CXX
AC_PROG_CPP
m4_ifdef([AM_PROG_AR], [AM_PROG_AR]) dnl Workaround for Automake 1.11

AC_LIBTOOL_WIN32_DLL
AC_DISABLE_STATIC
AC_PROG_LIBTOOL

AM_PROG_CC_C_O
AX_GCC_FLAGS([-Wall -Werror=uninitialized -std=c99])

AC_PROG_MKDIR_P
AM_PROG_LEX
AX_PROG_AWK
AX_PROG_GROFF
AC_PATH_PROGS([M4], [gm4 m4], [m4])

#####################################################################
# Internationalisation
#####################################################################

AM_NLS
AM_GNU_GETTEXT([external])
AM_GNU_GETTEXT_VERSION([0.18])
AX_DESKTOP_I18N

#####################################################################
# Check for libraries that use pkg-config
#####################################################################

PKG_PROG_PKG_CONFIG

AX_CHECK_GUILE([2.0.0])

PKG_CHECK_MODULES(GLIB, [glib-2.0 >= 2.38.0], ,
  AC_MSG_ERROR([GLib 2.38.0 or later is required.]))
AC_DEFINE([G_DISABLE_DEPRECATED], [1], [Disable deprecated GLib features])

PKG_CHECK_MODULES(GIO, [gio-2.0 >= 2.38.0], ,
  AC_MSG_ERROR([GIO 2.38.0 or later is required.]))

dnl GTK2 is used by default, though GTK3 can be choosen instead
dnl with the --with-gtk3 configure option.
AC_MSG_CHECKING([whether to use GTK3])
AC_ARG_WITH([gtk3],
  AS_HELP_STRING([--with-gtk3], [Build with GTK3.]))

if test "X$with_gtk3" = "Xyes"; then
  AC_MSG_RESULT([yes])
  AC_DEFINE([ENABLE_GTK3], [1],
    [Define to 1 if GTK3 will be used for build.])
else
  AC_MSG_RESULT([no])
fi

AS_CASE(["$with_gtk3"],
dnl Use GTK3.
  [yes],
    [PKG_CHECK_MODULES(GTK, [gtk+-3.0 >= 3.0.0],
      [WITH_GTK3=1],
      AC_MSG_ERROR([GTK+ 3.0.0 or later is required.]))],
dnl Default case: use GTK2.
  [PKG_CHECK_MODULES(GTK, [gtk+-2.0 >= 2.24.0],
    [WITH_GTK3=0],
    AC_MSG_ERROR([GTK+ 2.24.0 or later is required.]))])

AM_CONDITIONAL([ENABLE_GTK3], [test "x$WITH_GTK3" = x1])

AS_CASE(["$with_gtk3"],
dnl Use GDK3.
  [yes],
    [PKG_CHECK_MODULES(GDK, [gdk-3.0 >= 3.0.0], ,
      AC_MSG_ERROR([GDK 3.0.0 or later is required.]))],
dnl Default case: use GDK2.
  [PKG_CHECK_MODULES(GDK, [gdk-2.0 >= 2.24.0], ,
    AC_MSG_ERROR([GDK+ 2.24.0 or later is required.]))])

PKG_CHECK_MODULES(GDK_PIXBUF, [gdk-pixbuf-2.0 >= 2.21.0], ,
  AC_MSG_ERROR([GDK_PIXBUF 2.21.0 or later is required.]))

AX_CHECK_CAIRO

PKG_CHECK_MODULES(PANGOCAIRO, [pangocairo >= 0.26], ,
  AC_MSG_ERROR([PANGOCAIRO 0.26 or later is required.]))

PKG_CHECK_MODULES(PANGO, [pango >= 1.23.0], ,
  AC_MSG_ERROR([PANGO 1.23.0 or later is required.]))

#####################################################################
# Header files & particular functions
#####################################################################

AC_HEADER_SYS_WAIT

# FIXME these checks are obsolete, because every plausible platform
# provides what they check for.
AC_HEADER_STDC

# FIXME these checks may be pointless for one of three reasons: every
# even vaguely sane libc implementation provides them; we don't use
# any of their functions in favour of GLib alternatives; or we don't
# provide an alternative, so the build will fail anyway.
AC_CHECK_HEADERS([math.h stdlib.h string.h unistd.h])

# FIXME since we don't have a sensible alternative, if these headers
# are missing we should fail. Also need to remove HAVE_ERRNO_H tests
# in the C source code, since if we *don't* have it the build will
# fail anyway.
AC_CHECK_HEADERS([errno.h fcntl.h])

# FIXME On a system without locale.h, the user may have just disabled NLS
# to be able to build.  But are there known systems with working NLS but
# without a locale.h?  We do need to include locale.h on some systems
# to be able to build libleptongui/src/lepton-schematic.c
AC_CHECK_HEADERS([locale.h])

# Check for lrint in math library.
AC_CHECK_LIB([m], [lrint],
             AC_DEFINE([HAVE_LRINT], 1,
                       [If your math library has lrint in it, define this]))

AC_CHECK_LIB([m], [atan2])

AC_CHECK_FUNCS([chown getlogin])

# Check if the getopt header is present
AC_CHECK_HEADERS([getopt.h])
# Check for getopt_long
# On FreeBSD the 'gnugetopt' library is needed.
AC_SEARCH_LIBS([getopt_long], [gnugetopt],
               AC_DEFINE([HAVE_GETOPT_LONG], 1,
                         [Define to 1 if you have the `getopt_long' function.]))

# Check for misc features of awk
AX_AWK_FEATURES

#####################################################################
# Optional things
#####################################################################

# Assertions
AX_OPTION_ASSERT
# Viewing documentation
AX_OPTION_URI_VIEWER
# API documentation
AX_OPTION_DOXYGEN
# XDG desktop files
AX_OPTION_XDG_DATA
# XDG database updates
AX_OPTION_XDG_DB
# libstroke support
AX_OPTION_STROKE
# attrib
AX_OPTION_ATTRIB
# contributed software
AX_OPTION_CONTRIB

#####################################################################
# Tool-specific setup
#####################################################################

# Where should data files be installed/searched for?
AX_DATA_DIRS
# Where should PCB footprints be searched for?
AX_PCB_DIRS
# Set up liblepton with the correct ld version number
AX_LIBLEPTON([8:0:0])
# Set up libleptonattrib with the correct ld version number
AX_LIBLEPTONATTRIB([3:0:0])
# Set up libleptongui with the correct ld version number.
AX_LIBLEPTONGUI([2:0:0])

#####################################################################
# Generate output
#####################################################################

AC_CONFIG_FILES([Makefile

                 liblepton/Makefile
                 liblepton/liblepton.pc
                 liblepton/po/Makefile.in
                 liblepton/data/Makefile
                 liblepton/docs/Makefile
                 liblepton/docs/images/Makefile
                 liblepton/include/Makefile
                 liblepton/lib/Makefile
                 liblepton/scheme/Makefile
                 liblepton/src/Makefile
                 liblepton/tests/Makefile

                 utils/cli/Makefile
                 utils/cli/src/Makefile
                 utils/cli/docs/Makefile
                 utils/cli/po/Makefile.in

                 libleptongui/Makefile
                 libleptongui/libleptongui.pc
                 libleptongui/po/Makefile.in
                 libleptongui/docs/Makefile
                 libleptongui/include/Makefile
                 libleptongui/src/Makefile
                 libleptongui/examples/Makefile
                 libleptongui/lib/Makefile
                 libleptongui/data/Makefile
                 libleptongui/bitmap/Makefile
                 libleptongui/tests/Makefile
                 libleptongui/scheme/Makefile

                 schematic/Makefile

                 libleptonattrib/libleptonattrib.pc
                 libleptonattrib/Makefile
                 libleptonattrib/po/Makefile.in
                 libleptonattrib/include/Makefile
                 libleptonattrib/src/Makefile
                 libleptonattrib/data/Makefile
                 libleptonattrib/docs/Makefile

                 utils/attrib/Makefile

                 utils/symcheck/tests/Makefile
                 utils/symcheck/docs/Makefile
                 utils/symcheck/Makefile

                 utils/netlist/Makefile
                 utils/netlist/scheme/Makefile
                 utils/netlist/examples/Makefile
                 utils/netlist/tests/Makefile
                 utils/netlist/docs/Makefile
                 utils/netlist/docs/vams/Makefile
                 utils/netlist/examples/spice-noqsi/Makefile
                 utils/netlist/examples/vams/Makefile
                 utils/netlist/examples/vams/vhdl/Makefile
                 utils/netlist/examples/vams/vhdl/basic-vhdl/Makefile
                 utils/netlist/examples/vams/vhdl/new-vhdl/Makefile
                 utils/netlist/examples/switcap/Makefile
                 utils/netlist/examples/verilog/Makefile
                 utils/netlist/examples/verilog/T_FF_example/Makefile
                 utils/netlist/examples/verilog/T_FF_example/verilog_modules/Makefile
                 utils/netlist/examples/analog/Makefile
                 utils/netlist/examples/analog/varactor_osc/Makefile
                 utils/netlist/examples/analog/bandpass/Makefile
                 utils/netlist/examples/analog/voltage_doubler/Makefile

                 utils/Makefile

                 utils/symfix/Makefile
                 utils/symfix/docs/Makefile

                 utils/archive/docs/Makefile
                 utils/archive/Makefile

                 utils/embed/Makefile
                 utils/embed/docs/Makefile

                 utils/pcb_backannotate/Makefile
                 utils/pcb_backannotate/docs/Makefile

                 utils/refdes_renum/Makefile
                 utils/refdes_renum/docs/Makefile
                 utils/refdes_renum/tests/Makefile
                 utils/refdes_renum/tests/inputs/Makefile
                 utils/refdes_renum/tests/outputs/Makefile

                 utils/schdiff/Makefile
                 utils/schdiff/docs/Makefile

                 utils/upcfg/Makefile
                 utils/upcfg/docs/Makefile

                 utils/sch2pcb/Makefile
                 utils/sch2pcb/src/Makefile
                 utils/sch2pcb/examples/Makefile
                 utils/sch2pcb/docs/Makefile

                 utils/tragesym/Makefile
                 utils/tragesym/examples/Makefile
                 utils/tragesym/docs/Makefile

                 symbols/Makefile
                 symbols/docs/Makefile
                 symbols/scheme/Makefile
                 symbols/sym/Makefile
                 symbols/sym-gnetman/Makefile
                 symbols/sym-verilog/Makefile
                 symbols/sym-vhdl/Makefile

                 docs/Makefile
                 docs/changelogs/Makefile
                 docs/manual/Makefile
                 docs/scheme-api/Makefile
                 docs/wiki/Makefile
                 docs/news/Makefile
                 docs/design/Makefile

                 examples/Makefile
                 examples/RF_Amp/Makefile
                 examples/TwoStageAmp/Makefile
                 examples/gTAG/Makefile
                 examples/lightning_detector/Makefile

                 contrib/Makefile
                 contrib/smash_megafile/Makefile
                 contrib/olib/Makefile
                 contrib/gmk_sym/Makefile
                 contrib/gmk_sym/examples/Makefile
                 contrib/sarlacc_schem/Makefile
                 contrib/scripts/Makefile
                 contrib/scripts/sw2asc
                 contrib/renum/Makefile
                 contrib/renum/src/Makefile
                 contrib/renum/docs/Makefile
])
AC_OUTPUT
