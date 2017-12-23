AC_INIT(vpnsw.pl,0.06)
AM_INIT_AUTOMAKE
AC_PREFIX_DEFAULT(/usr/local)

PACKAGE=vpnsw

AC_CHECK_PROG(HAVE_PERL, perl, true, false, /usr/local/bin /usr/bin)
if test "x$HAVE_PERL" = "xfalse"; then
	AC_MSG_ERROR([Requested program perl not found])
fi
AC_SUBST(PERL, perl)
AC_PATH_PROG([PERL],[perl])

AC_PROG_INSTALL

AC_CANONICAL_HOST

case $host_os in
    *freebsd* )
	AC_SUBST(ROOT_GROUP, "wheel")
	AM_CONDITIONAL(FREEBSD_OS, true)
	AM_CONDITIONAL(LINUX_OS, false)
	OSNAME=freebsd
	ROOT_GROUP=wheel
        ;;
    *linux* )
	AC_SUBST(ROOT_GROUP, "root")
	AM_CONDITIONAL(FREEBSD_OS, false)
	AM_CONDITIONAL(LINUX_OS, true)
	OSNAME=linux
	ROOT_GROUP=root
        ;;
esac

AM_CONDITIONAL(SYSTEMD, false)
if test -d /lib/systemd/system; then
	AM_CONDITIONAL(SYSTEMD, true)
fi

test "x$prefix" == "xNONE" && prefix=$ac_default_prefix

AC_ARG_WITH(confdir,
	AS_HELP_STRING([--with-confdir=PATH],[set configuration dir to PATH (default: "${ac_default_prefix}"/etc/${PACKAGE})]),
	[ if test ! -z "$with_confdir" ; then
		case $with_confdir in
			/*)
				APP_CONFDIR="$with_confdir"
				;;
			*)
                                AC_MSG_ERROR(You must specify an absolute path to --with-confdir=PATH)
				;;
		esac
	else
		APP_CONFDIR="$prefix/etc/${PACKAGE}"
	fi ],
	[
	APP_CONFDIR="$prefix/etc/${PACKAGE}"
	])

test "x$APP_CONFDIR" == "x/usr/etc/${PACKAGE}" && APP_CONFDIR="/etc/${PACKAGE}"
test "x$prefix" == "x/usr" && sysconfdir="/etc"


AC_DEFINE_UNQUOTED(APP_CONFDIR, "$APP_CONFDIR", [location of configuration files for ${PACKAGE}])
AC_SUBST(APP_CONFDIR, "$APP_CONFDIR")

AC_ARG_WITH(logdir,
	AS_HELP_STRING([--with-logdir=PATH],[set file path for source logdir (default: /var/log/${PACKAGE}/${PACKAGE}.log)]),
	[ if test ! -z "$with_logdir" ; then
		case $with_logdir in
			/*)
				APP_LOGDIR="$with_logdir"
				;;
			*)
                                AC_MSG_ERROR(You must specify an absolute path to --with-logdir=PATH)
				;;
		esac
	else
		APP_LOGDIR="/var/log/${PACKAGE}"
	fi ],
	[ 
	APP_LOGDIR="/var/log/${PACKAGE}"
	])

AC_DEFINE_UNQUOTED(APP_LOGDIR, "$APP_LOGDIR", [location of ${PACKAGE} logdir])
AC_SUBST(APP_LOGDIR, "$APP_LOGDIR")

AC_ARG_WITH(rundir,
	AS_HELP_STRING([--with-rundir=PATH],[set file path for source rundir (default: /var/run/${PACKAGE})]),
	[ if test ! -z "$with_rundir" ; then
		case $with_rundir in
			/*)
				APP_RUNDIR="$with_rundir"
				;;
			*)
                                AC_MSG_ERROR(You must specify an absolute path to --with-rundir=PATH)
				;;
		esac
	else
		APP_RUNDIR="/var/run/${PACKAGE}"
	fi ],
	[
	APP_RUNDIR="/var/run/${PACKAGE}"
	])

AC_DEFINE_UNQUOTED(APP_RUNDIR, "$APP_RUNDIR", [location of pid file])
AC_SUBST(APP_RUNDIR, "$APP_RUNDIR")

case $host_os in
    *freebsd* )
	default_user="root"
	default_group="wheel"
        ;;
    *linux* )
	default_user="root"
	default_group="root"
        ;;
esac

AC_ARG_WITH(user,
	AS_HELP_STRING([--with-user=${PACKAGE}],[set executing user name]),
	[ if test ! -z "$with_user" ; then
		case $with_user in
			"")
				AC_MSG_ERROR(You must specify user name)
				;;
			*)
				APP_USER="$with_user"
				;;
		esac
	else
		APP_USER="$default_user"
	fi ],
	[ APP_USER="$default_user" ])
AC_DEFINE_UNQUOTED(APP_USER, "$APP_USER", [effective user])
AC_SUBST(APP_USER, "$APP_USER")

AC_ARG_WITH(group,
	AS_HELP_STRING([--with-group=${PACKAGE}],[set executing group name]),
	[ if test ! -z "$with_group" ; then
		case $with_group in
			"")
				AC_MSG_ERROR(You must specify group name)
				;;
			*)
				APP_GROUP="$with_group"
				;;
		esac
	else
		APP_GROUP="$default_group"
	fi ],
	[ APP_GROUP="$default_group" ])
AC_DEFINE_UNQUOTED(APP_GROUP, "$APP_GROUP", [effective group id])
AC_SUBST(APP_GROUP, "$APP_GROUP")


AC_DEFINE_UNQUOTED(APP_LIBDIR, ${prefix}/share/${PACKAGE}, [application lib directory])
AC_SUBST(APP_LIBDIR, ${prefix}/share/${PACKAGE})


AC_DEFUN([AC_PERL_MODULES],[
ac_perl_modules="$1"
for ac_perl_module in $ac_perl_modules; do
AC_MSG_CHECKING(for perl module $ac_perl_module)
perl "-M$ac_perl_module" -e exit > /dev/null 2>&1
if test $? -ne 0; then
    AC_MSG_RESULT(no);
    AC_MSG_ERROR(You must install perl module $ac_perl_module)
  else
    AC_MSG_RESULT(ok);
fi
done])

AC_PERL_MODULES([
POSIX
Apache::Htpasswd
File::Basename
File::stat
Sys::Hostname
Mojo::Base
Mojo::Home
Mojo::IOLoop::Subprocess
Mojo::JSON
Mojo::Server::Prefork
Mojo::UserAgent
Mojo::Util
])

AC_OUTPUT([
Makefile 
vpnsw:vpnsw.pl
vpnsw.service
rc.d/vpnsw
init.d/vpnsw
])

dnl EOF


