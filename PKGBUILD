pkgname=openerp61_ru-bzr
pkgver=4264
pkgrel=1
pkgdesc="Advanced OpenSource ERP and CRM server, bazaar version + alternative russification"
url=http://openerp.com/
arch=(i686 x86_64)
license=(GPLv3)
conflicts=('openerp'
	'openerp-server'
	'openerp-web')
provides=('openerp')
depends=('postgresql'
	'python2-dateutil'
	'python2-feedparser'
	'python-imaging'
	'python-gdata'
	'python2-ldap'
	'python2-lxml'
	'python2-mako'
	'python2-openid'
	'python2-psycopg2'
	'python-babel'
	'python-pychart'
	'pydot'
	'python2-pyparsing'
	'python-reportlab'
	'python2'
	'python-simplejson'
	'python2-pytz'
	'python2-vatnumber'
	'python2-vobject'
	'python2-pywebdav'
	'python2-werkzeug'
	'python2-xlwt'
	'python2-yaml'
	'zsi')
makedepends=('bzr')
source=(
"https://github.com/downloads/v-k/vak_files/Lin-6.1.tar.bz2"
kaban22_base_module_import_view.patch
openerp.rc
openerp.confd
)
install=openerp.install

_bzrtrunk="lp:openobject-server/6.1"
_bzrmod="openobject-server"

_bzrtrunkaddons="lp:openobject-addons/6.1"
_bzrmodaddons="openobject-addons"

_bzrtrunkweb="lp:openerp-web/6.1"
_bzrmodweb="openerp-web"

build()
{
  #Branch or merge project to baraar
  cd $srcdir

  if [[ -d "$srcdir/$_bzrmod/.bzr" ]]; then
    (cd "$_bzrmod" && bzr pull $_bzrtrunk)
  else
    bzr branch $_bzrtrunk $_bzrmod
  fi

  if [[ -d "$srcdir/$_bzrmodaddons/.bzr" ]]; then
    (cd "$_bzrmodaddons" && bzr pull $_bzrtrunkaddons)
  else
    bzr branch $_bzrtrunkaddons $_bzrmodaddons
  fi

  if [[ -d "$srcdir/$_bzrmodweb/.bzr" ]]; then
    (cd "$_bzrmodweb" && bzr pull $_bzrtrunkweb)
  else
    bzr branch $_bzrtrunkweb $_bzrmodweb
  fi

  #Copy all code to build catalog
  if [[ -d "$srcdir/build" ]]; then
    (rm -rf "$srcdir/build" && mkdir "$srcdir/build")
  else
    mkdir "$srcdir/build"
  fi

  cp -r $srcdir/$_bzrmod/* "$srcdir/build"
  cp -r $srcdir/$_bzrmodaddons/* "$srcdir/build/openerp/addons"
  cp -r $srcdir/$_bzrmodweb/addons/* "$srcdir/build/openerp/addons"

  #Alternative russification
  chmod -R 755 $srcdir/Lin-6.1
  cd $srcdir/Lin-6.1
  chmod 644 `find | grep ru.po`
  chmod 644 `find | grep openerp-client.mo`
  cp -rf $srcdir/Lin-6.1/Server/server/openerp/addons/* "$srcdir/build/openerp/addons"

  #Start build project
  cd ${srcdir}/build

  # patch base_module_import_view.xml
  patch -Np0 -i ${srcdir}/kaban22_base_module_import_view.patch || return 1

  # Force package data inclusion
  sed -i -e s/#include_package_data/include_package_data/ setup.py
  python2 setup.py install --root="${pkgdir}"
  rm -rf ${pkgdir}/usr/openerp

  # Add server configs
  mkdir ${pkgdir}/etc/{rc.d,conf.d,openerp,logrotate.d} -p
  install -Dm 755 ${srcdir}/openerp.rc ${pkgdir}/etc/rc.d/openerp-server
  install -Dm 644 ${srcdir}/openerp.confd ${pkgdir}/etc/conf.d/openerp-server

  cd ${srcdir}/build/install/

  install -Dm 644 openerp-server.conf ${pkgdir}/etc/openerp/openerp-server.conf
  install -Dm 644 openerp-server.logrotate ${pkgdir}/etc/logrotate.d/openerp-server
}
md5sums=('803a1e7357142f9b2ffc2e6d16963871'
         '3cb55c2294d0895423247989a53bb62a'
         'e7dc5e9b048ea206b02634587a9a7e45'
         'f88d49de3dae4bbe4f0a41ebb53e52ff')
