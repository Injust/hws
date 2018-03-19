arch=(x86_64)
depends=(openssl pcre zlib)
makedepends=(git make patch)
pkgdesc="Lightweight HTTP server and IMAP/POP3 proxy server"
pkgname=nginx
pkgrel=1
pkgver=1.13.9

_build_parameters=(
	--add-module=../incubator-pagespeed-ngx-latest-beta
	--add-module=../ngx_brotli
	--conf-path=/etc/nginx/nginx.conf
	--error-log-path=stderr
	--http-client-body-temp-path=/var/lib/nginx/client-body
	--http-proxy-temp-path=/var/lib/nginx/proxy
	--prefix=/etc/nginx
	--sbin-path=/usr/bin/nginx
	--with-cc-opt="-DTCP_FASTOPEN=23"
	--with-file-aio
	--with-http_realip_module
	--with-http_ssl_module
	--with-http_v2_hpack_enc
	--with-http_v2_module
	--with-pcre-jit
	--with-threads
	--without-http_access_module
	--without-http_auth_basic_module
	--without-http_browser_module
	--without-http_charset_module
	--without-http_empty_gif_module
	--without-http_fastcgi_module
	--without-http_geo_module
	--without-http_limit_conn_module
	--without-http_limit_req_module
	--without-http_map_module
	--without-http_memcached_module
	--without-http_mirror_module
	--without-http_referer_module
	--without-http_scgi_module
	--without-http_split_clients_module
	--without-http_ssi_module
	--without-http_upstream_hash_module
	--without-http_upstream_ip_hash_module
	--without-http_upstream_keepalive_module
	--without-http_upstream_least_conn_module
	--without-http_upstream_zone_module
	--without-http_userid_module
	--without-http_uwsgi_module
	--without-mail_imap_module
	--without-mail_pop3_module
	--without-mail_smtp_module
	--without-poll_module
	--without-select_module
	--without-stream_access_module
	--without-stream_geo_module
	--without-stream_limit_conn_module
	--without-stream_map_module
	--without-stream_return_module
	--without-stream_split_clients_module
	--without-stream_upstream_hash_module
	--without-stream_upstream_least_conn_module
	--without-stream_upstream_zone_module
)

sha384sums=(
	0f62be93959eb5df320c9018a0bac69c6f8a167bf838f303751190be10a06384ae10d5726e1fbdec448f1c209d6197c7
	46a0bd7d28e7187eb138cc00dcd79caccbe42dba5ca19e07597403a52efb7557ed77944253c92c95a75da94f1856e1d7
	508e29d01a1859427a5a846a527c3f26580428b3edae1937db688d2c4965977d667c202b13621877cd0a41f5faaf1ae5
	8a37dd16e3eb384eaa429d10e7576df6d09fec45a55b17dd45bfb532b59b52e9d440681ea80e828d463c4dbbe401828e
	9f775e455bc6adf1d83dddb4fc90ce935f772dd0ddf81e5dfab9d4af53aa395831d810a1aa2590f0dfe3ac979dd03639
	bdcabfa111bb89e5a64376c05e27c0752602b321796a4e7b221d64694b502a0b3cd65d82fae73381f8112cc5332c0244
	d66c9b53d50a1cb5a6ef43202f5e14c25998bc4d47fb460be11e2021bb5271bf07f5f473d51918ed7c009478a13704fd
	f9556d5e1fe8f9745e25685bbe60e075ebd6463923ec476467d630ad7ecfa28b81dec65216f0c4f773a997ac779d301c
	22a2e24c3bd9aa4e15e828d9d1d0eb670f669640c14481abdb63494a04770f304d7e0a1134ae79658bc2daf8f4793551
	SKIP
	edd4a784a34f783ea57a6792c24ab1a6bbf0e8242a5ed1abefbbf5a338f8779f91b1e5bc7ecd348681ece36a7ad43aa7
)

source=(
	incubator-pagespeed-ngx-latest-beta.tar.gz::https://codeload.github.com/apache/incubator-pagespeed-ngx/tar.gz/latest-beta
	logrotate
	nginx-${pkgver}.tar.gz::https://nginx.org/download/nginx-${pkgver}.tar.gz
	nginx__204-headers.patch
	nginx__autoindex-epoch.patch
	nginx__autoindex-fts.patch
	nginx__dynamic-tls-records.patch
	nginx__http2-hpack.patch
	nginx__server-header.patch
	ngx_brotli::git+https://github.com/google/ngx_brotli.git
	service
)

prepare() {
	cd "${srcdir}/incubator-pagespeed-ngx-latest-beta"
	curl "$(scripts/format_binary_url.sh PSOL_BINARY_URL)" | tar xz

	cd "${srcdir}/ngx_brotli"
	git submodule update --init

	cd "${srcdir}/${pkgname}-${pkgver}"
	cat "${srcdir}/${pkgname}__"*.patch | patch -p1
}

build() {
	cd "${srcdir}/${pkgname}-${pkgver}"
	./configure ${_build_parameters[@]}
	make
}

package() {
	cd "${srcdir}/${pkgname}-${pkgver}"
	make "DESTDIR=${pkgdir}" install

	rm -r "${pkgdir}/etc/${pkgname}"

	install -Dm644 "${srcdir}/logrotate" "${pkgdir}/etc/logrotate.d/${pkgname}"
	install -Dm644 "${srcdir}/service" "${pkgdir}/usr/lib/systemd/system/${pkgname}.service"
	for _file in "${srcdir}/${pkgname}-${pkgver}/contrib/vim/"*; do
		install -Dm644 "${_file}/${pkgname}.vim" "${pkgdir}/usr/share/vim/vimfiles/${_file##*/}/${pkgname}.vim"
	done
}
