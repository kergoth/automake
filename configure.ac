# Process this file with autoconf to produce a configure script.

# Copyright (C) 1995, 1996, 1997, 1998, 1999, 2000, 2001, 2002, 2003,
# 2004, 2006, 2007, 2008, 2009, 2010, 2011 Free Software Foundation,
# Inc.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2, or (at your option)
# any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

AC_INIT([GNU Automake], [1.11a], [bug-automake@gnu.org])

m4_ifndef([AC_PACKAGE_URL],
	  [AC_SUBST([PACKAGE_URL], [http://www.gnu.org/software/automake/])])

AC_CONFIG_SRCDIR(automake.in)
AC_CONFIG_AUX_DIR(lib)

AC_CANONICAL_HOST
AC_CANONICAL_BUILD

# Save the AUTOCONF setting before AM_INIT_AUTOMAKE overrides it; this
# way we can run Autoconf tests from configure (or from the test
# suite) without being bothered by `missing'.  Likewise for autom4te,
# autoreconf, autoheader, and autoupdate.
AC_SUBST([am_AUTOCONF], ["${AUTOCONF-autoconf}"])
AC_SUBST([am_AUTOM4TE], ["${AUTOM4TE-autom4te}"])
AC_SUBST([am_AUTORECONF], ["${AUTORECONF-autoreconf}"])
AC_SUBST([am_AUTOHEADER], ["${AUTOHEADER-autoheader}"])
AC_SUBST([am_AUTOUPDATE], ["${AUTOUPDATE-autoupdate}"])

AM_INIT_AUTOMAKE([1.10a dist-bzip2 filename-length-max=99 color-tests
		  parallel-tests silent-rules])

# The API version is the base version.  We must guarantee
# compatibility for all releases with the same API version.
# Our current rule is that:
# * All releases, including the prereleases, in an X.Y series
#   are compatible.  So 1.5.1c is compatible with 1.5.
# * Prereleases on the trunk are all incompatible -- 1.5b and 1.5c
#   aren't the same.
APIVERSION=`echo "$VERSION" | sed -e 's/^\([[0-9]]*\.[[0-9]]*[[a-z]]*\).*$/\1/'`
AC_SUBST([APIVERSION])

AC_SUBST([pkgvdatadir], ["\${datadir}/$PACKAGE-$APIVERSION"])
AC_SUBST([scriptdir], ["\${pkgvdatadir}"])
AC_SUBST([amdir], ["\${pkgvdatadir}/am"])
AC_SUBST([automake_acdir], ["\${datadir}/aclocal-$APIVERSION"])
AC_SUBST([system_acdir], ["\${datadir}/aclocal"])

# $AUTOMAKE and $ACLOCAL are always run after a `cd $top_srcdir',
# hence `.' is really what we want for perllibdir, libdir, and acdir.
ACLOCAL="perllibdir=\"`pwd`/lib$PATH_SEPARATOR./lib\" \"`pwd`/aclocal\" --acdir=m4 -I m4"
AUTOMAKE="perllibdir=\"`pwd`/lib$PATH_SEPARATOR./lib\" \"`pwd`/automake\" --libdir=lib"

AC_PATH_PROG(PERL, perl)
if test -z "$PERL"; then
   AC_MSG_ERROR([perl not found])
fi
$PERL -e 'require 5.006;' || {
   AC_MSG_ERROR(
[perl 5.6 or better is required; perl 5.8.2 or better
is recommended.  If you have several perl versions
installed, select the one Automake should use using
  ./configure PERL=/path/to/perl])
}

# We require ithreads support, and version 5.7.2 for CLONE.
AC_CACHE_CHECK([whether $PERL supports ithreads], [am_cv_prog_PERL_ithreads],
[if $PERL -e '
    require 5.007_002;
    use Config;
    if ($Config{useithreads})
      {
	require threads;
	import threads;
	require Thread::Queue;
	import Thread::Queue;
	exit 0;
      }
    exit 1;' >&AS_MESSAGE_LOG_FD 2>&1
then
  am_cv_prog_PERL_ithreads=yes
else
  am_cv_prog_PERL_ithreads=no
fi])
if test $am_cv_prog_PERL_ithreads = yes; then
  PERL_THREADS=1;
else
  PERL_THREADS=0;
fi
AC_SUBST([PERL_THREADS])

# The test suite will skip some tests if tex is absent.
AC_CHECK_PROG([TEX], [tex], [tex])

# Generate man pages.
AM_MISSING_PROG([HELP2MAN], [help2man])

# Test for Autoconf.  We run Autoconf in a subdirectory to ease
# deletion of any files created (such as those added to
# autom4te.cache).  We used to perform only the last of the three
# following tests, but some users were unable to figure out that their
# installation was broken since --version appeared to work.

required_autoconf_version=2.62
AC_CACHE_CHECK([whether autoconf is installed], [am_cv_autoconf_installed],
[if AM_RUN_LOG([eval $am_AUTOCONF --version]);
then
  am_cv_autoconf_installed=yes
else
  am_cv_autoconf_installed=no
fi])
if test "$am_cv_autoconf_installed" = no; then
  AC_MSG_ERROR([Autoconf $required_autoconf_version or better is required.
    Please make sure it is installed and in your PATH.])
fi

AC_CACHE_CHECK([whether autoconf works], [am_cv_autoconf_works],
[mkdir conftest
echo 'AC''_INIT' > conftest/conftest.ac
if AM_RUN_LOG([cd conftest && eval $am_AUTOCONF -o /dev/null conftest.ac]);
then
  am_cv_autoconf_works=yes
else
  am_cv_autoconf_works=no
fi
rm -rf conftest])
if test "$am_cv_autoconf_works" = no; then
  AC_MSG_ERROR([The installed version of autoconf does not work.
    Please check config.log for error messages before this one.])
fi

AC_CACHE_CHECK([whether autoconf is recent enough], [am_cv_autoconf_version],
[mkdir conftest
dnl Creative quoting required to avoid spurious expansion of AC_PREREQ macro
echo 'AC'"_PREREQ([[$required_autoconf_version]])" > conftest/conftest.ac
if AM_RUN_LOG([cd conftest && eval $am_AUTOCONF -o /dev/null conftest.ac]);
then
  am_cv_autoconf_version=yes
else
  am_cv_autoconf_version=no
fi
rm -rf conftest])
if test "$am_cv_autoconf_version" = no; then
  AC_MSG_ERROR([Autoconf $required_autoconf_version or better is required.])
fi

# Test for ln.  We need use it to install the versioned binaries.
AC_MSG_CHECKING([whether ln works])
AC_CACHE_VAL([am_cv_prog_ln], [
rm -f conftest conftest.file
: >conftest.file
if ln conftest.file conftest 2>/dev/null; then
  am_cv_prog_ln=ln
else
  am_cv_prog_ln='cp -p'
fi
rm -f conftest conftest.file])
AC_SUBST([LN], [$am_cv_prog_ln])
result=no
test "x$am_cv_prog_ln" = xln && result=yes
AC_MSG_RESULT([$result])

# The amount we should wait after modifying files depends on the platform.
# On Windows '95, '98 and ME, files modifications have 2-seconds
# granularity and can be up to 3 seconds in the future w.r.t. the
# system clock.  When it is important to ensure one file is older
# than another we wait at least 5 seconds between creations.
case $build in
  *-pc-msdosdjgpp) MODIFICATION_DELAY=5;;
  *)               MODIFICATION_DELAY=2;;
esac
AC_SUBST([MODIFICATION_DELAY])

## ------------------------------------------- ##
##  Test for things needed by the test suite.  ##
## ------------------------------------------- ##

AC_PROG_EGREP
AC_PROG_FGREP

dnl FIXME: could we extract this in a simpler way through autoconf
dnl FIXME: idioms or internals?
AC_DEFUN(
  [_AM_INIT_BOURNE_COMPATIBLE_VAR],
  [am_bourne_compatible="AS_ESCAPE(_m4_expand([AS_BOURNE_COMPATIBLE]))"])

dnl
dnl Arguments to this macro:
dnl
dnl   $1 - shell to test
dnl   $2 - description of the tested feature
dnl   $3 - shell code used to check the feature; to indicate success,
dnl        it can either exit with status 77, or have the last command
dnl        returning with exit status of zero
dnl   $4 - shell code to execute if the check on the shell is successful
dnl        (defaults to nothing)
dnl   $5 - shell code to execute if the check on the shell is not
dnl        successful (defaults to nothing)
dnl
AC_DEFUN([_AM_CHECK_SHELL_FEATURE],
  [AC_REQUIRE([_AM_INIT_BOURNE_COMPATIBLE_VAR])
  AC_MSG_CHECKING([whether $1 $2])
  if { $1 -c "$am_bourne_compatible
AS_ESCAPE([$3])
test \$? -eq 0 || exit 1
# Use 77 to indicate success (rather than 0), in case some shell
# acts like Solaris 10's /bin/sh, exiting successfully on some
# syntax errors.
exit 77" >&AS_MESSAGE_LOG_FD 2>&1; test $? -eq 77; }
  then
    AC_MSG_RESULT([yes])
    $4
  else
    AC_MSG_RESULT([no])
    $5
  fi])

# AM_CHECK_CANDIDATE_TEST_SHELL(SHELL-PATH)
# -----------------------------------------
#
# Check if the given shell is good enough to run our test scripts.
# Inspired to gnulib's `tests/init.sh'.
#
# We require POSIX and XSI features (e.g., `$(...)' for command
# substitutions, `$((...))' for shell arithmetic, and support for
# ${var#...} and ${var%...} parameter expansions).
#
# We require that the shell can correctly trap EXIT when `set -e' is in
# effect (OSF1/Tru64 sh failed to do so, see commit v1.10b-52-g9fe8259).
#
# We also prefer shells that, when `set -x' is in effect, do not also
# redirect traces upon stderr redirections.  For example,
#  $ set -x; echo x 2>file
# would emit "+ echo x" into file with older zsh versions.  Similarly,
#   $ set -x; P=1 true 2>file
# would emit "P=1" into file with /usr/xpg4/bin/sh from Solaris 10 and
# /bin/sh from SunOS 5.11 and OpenBSD 4.7.
#
# Finally, we look for weird bugs and portability problems mentioned in
# the Autoconf manual, and reject shells that suffers from them. (TODO)
#
# Use `$am_score' to indicate the degree of acceptability of the shell.
# A score of "10" means that the shell is good enough for our needs;
# a score of "9" means that the shell has some minor bugs or limitation,
# but is still (barely) acceptable for our uses.  Any other score means
# that the shell is broken or unfit.
#
AC_DEFUN([_AM_CHECK_CANDIDATE_SHELL],
  [am_score=10
  while :; do

    _AM_CHECK_SHELL_FEATURE([$1],
      [supports \$(cmd)],
      [test "$(echo x)" = x],
      [], [am_score=1; break])

    _AM_CHECK_SHELL_FEATURE([$1],
      [supports \$((expr))],
      [test $((1 + 2 * 3)) = 7],
      [], [am_score=1; break])

    _AM_CHECK_SHELL_FEATURE([$1],
      [supports \${var@%:@glob}],
      [v=a/b/c; test ${v@%:@*/} = b/c],
      [], [am_score=1; break])

    _AM_CHECK_SHELL_FEATURE([$1],
      [supports \${var@%:@@%:@glob}],
      [v=a/b/c; test ${v@%:@@%:@*/} = c],
      [], [am_score=1; break])

    _AM_CHECK_SHELL_FEATURE([$1],
      [supports \${var%glob}],
      [v=a.b.c; test ${v%.*} = a.b],
      [], [am_score=1; break])

    _AM_CHECK_SHELL_FEATURE([$1],
      [supports \${var%%glob}],
      [v=a.b.c; test ${v%%.*} = a],
      [], [am_score=1; break])

    _AM_CHECK_SHELL_FEATURE([$1],
      ["set -e" preserves exit traps],
      [set -e; trap 'exit $?' 0; (exit 77); exit 77],
      [], [am_score=1; break])

    _AM_CHECK_SHELL_FEATURE([$1],
      ["set -x" corrupts stderr],
      [(set -x; P=1 true 2>&3) 3>&1 2>/dev/null | grep P=1],
      [am_score=9], [])

    break
  done])

# These messages only goes to the config.log file.
AC_MSG_NOTICE([will now look for a sturdy POSIX shell, for our testsuite])

AC_CACHE_VAL(
  [ac_cv_AM_TEST_RUNNER_SHELL],
  [if test "$AM_TEST_RUNNER_SHELL"; then
    # Let the user override it.
    ac_cv_AM_TEST_RUNNER_SHELL=$AM_TEST_RUNNER_SHELL
  else
    ac_cv_AM_TEST_RUNNER_SHELL=no
    am_candidate_shells=${CONFIG_SHELL-}
    # For the benefit of Solaris.
    am_PATH=$PATH$PATH_SEPARATOR/usr/xpg4/bin$PATH_SEPARATOR/usr/xpg6/bin
    for am_sh in sh sh5 dash ash bash zsh ksh pdksh; do
      AC_PATH_PROG([am_candidate_sh], [$am_sh], [], [$am_PATH])
      if test -n "$am_candidate_sh"; then
        am_candidate_shells="$am_candidate_shells $am_candidate_sh"
      fi
      AM_SUBST_NOTMAKE([am_candidate_sh])
      # Must nullify these in order not to interfere with the checks in
      # the next loop.
      AS_UNSET([am_candidate_sh])
      AS_UNSET([ac_cv_path_am_candidate_sh])
    done
    AS_UNSET([am_PATH]) # Not required anymore
    for am_sh in $am_candidate_shells; do
      am_score=0
      _AM_CHECK_CANDIDATE_SHELL([$am_sh])
      if test $am_score -eq 9; then
        # The shell is barely acceptable for our needs.  We might
        # still find one that is even better, so continue looking.
        AC_MSG_NOTICE([shell $am_sh is acceptable, but we might do better])
        ac_cv_AM_TEST_RUNNER_SHELL=$am_sh
      elif test $am_score -eq 10; then
        AC_MSG_NOTICE([shell $am_sh is good enough, stop looking])
        ac_cv_AM_TEST_RUNNER_SHELL=$am_sh
        break
      fi
    done
  fi
  AM_TEST_RUNNER_SHELL=$ac_cv_AM_TEST_RUNNER_SHELL])

if test $AM_TEST_RUNNER_SHELL = no; then
  AC_MSG_FAILURE([m4_normalize([no POSIX shell found that is good
                                enough to be used in our testsuite])])
else
  AC_MSG_NOTICE([will use $AM_TEST_RUNNER_SHELL as the testsuite shell])
fi

AC_ARG_VAR([AM_TEST_RUNNER_SHELL],
           [a sturdy POSIX shell for our testsuite])

# FIXME: remove soon
AC_SUBST([sh_errexit_works], [yes])

## ---------------------- ##
##  Create output files.  ##
## ---------------------- ##

AC_CONFIG_FILES([
  Makefile
  doc/Makefile
  lib/Automake/Makefile
  lib/Automake/tests/Makefile
  lib/Makefile
  lib/am/Makefile
  m4/Makefile
  tests/Makefile
])
AC_CONFIG_LINKS([tests/defs:tests/defs])
AC_CONFIG_FILES([tests/aclocal-${APIVERSION}:tests/aclocal.in],
                [chmod +x tests/aclocal-${APIVERSION}],
	        [APIVERSION=$APIVERSION])
AC_CONFIG_FILES([tests/automake-${APIVERSION}:tests/automake.in],
                [chmod +x tests/automake-${APIVERSION}])

AC_OUTPUT
