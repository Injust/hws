pkgname=hws
pkgdesc="Hydronium Web Server"

arch=(i686 x86_64)
conflicts=(caddy nginx)
depends=(pcre zlib)
makedepends=(git)
opensslver=1.1.0g
pkgrel=1
pkgver=1.13.9
provides=(caddy nginx)

sha384sums=(
	SKIP
	224ffcdfe6e9f02f71d945352dfb936c276ec738c1bf252e435304683cabaa8479e6190a74876de2008b9c302708d02f
	0f62be93959eb5df320c9018a0bac69c6f8a167bf838f303751190be10a06384ae10d5726e1fbdec448f1c209d6197c7
	508e29d01a1859427a5a846a527c3f26580428b3edae1937db688d2c4965977d667c202b13621877cd0a41f5faaf1ae5
	4d74cc598faacd64ec90a6d59691ace30bcee62220ee03a5a64901b2fe62e99fe861bb5f9fdc8bb16f7abcfe65815f58
	46a0bd7d28e7187eb138cc00dcd79caccbe42dba5ca19e07597403a52efb7557ed77944253c92c95a75da94f1856e1d7
	8a37dd16e3eb384eaa429d10e7576df6d09fec45a55b17dd45bfb532b59b52e9d440681ea80e828d463c4dbbe401828e
	9f775e455bc6adf1d83dddb4fc90ce935f772dd0ddf81e5dfab9d4af53aa395831d810a1aa2590f0dfe3ac979dd03639
	bdcabfa111bb89e5a64376c05e27c0752602b321796a4e7b221d64694b502a0b3cd65d82fae73381f8112cc5332c0244
	d66c9b53d50a1cb5a6ef43202f5e14c25998bc4d47fb460be11e2021bb5271bf07f5f473d51918ed7c009478a13704fd
	f9556d5e1fe8f9745e25685bbe60e075ebd6463923ec476467d630ad7ecfa28b81dec65216f0c4f773a997ac779d301c
	22a2e24c3bd9aa4e15e828d9d1d0eb670f669640c14481abdb63494a04770f304d7e0a1134ae79658bc2daf8f4793551
	6b679a8f03b9930dc5ebb36a25f4f5d270d5a6118bfc447c77b1f2156ff7d8a25a916992295a98405276e687c321b00d
	482565b155f5d21b8ed6ef79cc863031378fb86ec172b42570babfa5af1b1f7ef1a10ce35f7fd56e48b787dcc43e8a4e
)

source=(
	git+https://github.com/google/ngx_brotli.git
	https://caddyserver.com/download/linux/amd64?license=personal
	https://codeload.github.com/apache/incubator-pagespeed-ngx/tar.gz/latest-beta
	https://nginx.org/download/nginx-$pkgver.tar.gz
	https://www.openssl.org/source/openssl-$opensslver.tar.gz
	logrotate
	nginx__204-headers.patch
	nginx__autoindex-epoch.patch
	nginx__autoindex-fts.patch
	nginx__dynamic-tls-records.patch
	nginx__http2-hpack.patch
	nginx__server-header.patch
	openssl__equal-preference.patch
	service
)

_build_parameters=(
	--add-module=../incubator-pagespeed-ngx-latest-beta
	--add-module=../ngx_brotli
	--conf-path=/etc/hws/nginx.conf
	--error-log-path=stderr
	--http-client-body-temp-path=/var/lib/hws/body
	--http-proxy-temp-path=/var/lib/hws/proxy
	--prefix=/etc/hws
	--sbin-path=/usr/bin/hws-nginx
	--with-cc-opt="-DTCP_FASTOPEN=23"
	--with-file-aio
	--with-http_addition_module
	--with-http_realip_module
	--with-http_ssl_module
	--with-http_v2_hpack_enc
	--with-http_v2_module
	--with-openssl=../openssl-$opensslver
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

prepare() {
	cd incubator-pagespeed-ngx-latest-beta
	curl "$(scripts/format_binary_url.sh PSOL_BINARY_URL)" | tar xz

	cd ../ngx_brotli
	git submodule update --init

	cd ../nginx-$pkgver
	cat ../nginx__*.patch | patch -p1

	cd ../openssl-$opensslver
	cat ../openssl__*.patch | patch -p1
}

build() {
	cd nginx-$pkgver
	./configure ${_build_parameters[@]}
	make
}

package() {
	cd nginx-$pkgver
	make DESTDIR="$pkgdir" install

	chmod 755 "$pkgdir"/usr/bin/hws-nginx
	rm -r "$pkgdir"/etc/hws

	install -D ../caddy "$pkgdir"/usr/bin/hws-caddy
	install -Dm644 ../logrotate "$pkgdir"/etc/logrotate.d/hws
	install -Dm644 ../service "$pkgdir"/usr/lib/systemd/system/hws.service

	for i in contrib/vim/*; do
		install -Dm644 $i/nginx.vim "$pkgdir"/usr/share/vim/vimfiles/$i##*//nginx.vim
	done
}
