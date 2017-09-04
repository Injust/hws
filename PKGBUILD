pkgname=hws
pkgdesc="Hydronium Web Server"

arch=(i686 x86_64)
conflicts=(caddy nginx)
depends=(pcre zlib)
makedepends=(git)
opensslver=1.1.0f
pkgrel=1
pkgver=1.13.4
provides=(caddy nginx)

sha384sums=(
	SKIP
	SKIP
	c6954091b8a8fdda83921d14396e856d78f4afa8d22a071878a6c5e0240eebc88188c85715bf81ad1eced63fc32146eb
	1a9eafe1589d8eff8da8ea755c7e4b468e7efec6dc7bbde113036ddbbf755f505ceb9625b5c9c1a87821a92102b3575a
	db1a5b5b6d1a2af00077901d7fa4d6db623c1ef034ea8ce38c90fb8255a59415d6525b3032ac907fdbd42885494cd989
	5039fac50d6374ced88a2bbb60462833d6b8e643a73dbc73c5db94a9a1853dab7a5b09c95df2ffc4c0904db4ef5e9ade
	46a0bd7d28e7187eb138cc00dcd79caccbe42dba5ca19e07597403a52efb7557ed77944253c92c95a75da94f1856e1d7
	d24ab1e2b9aa079cb5f244a0bd8cdb8499401936d5c27637937a3593559846e43aa77b2bef1952052aa88babadaae19d
	4bd26e40705565dcf47d95179c6b34c0b570c8924a73d57b782657d7d4a67bd1034d48325a49aca57fd6100e9fe1acc6
	2dc833b3c351e7f4afd1739f4188e9e6dd3931735d0bf38c11d9c53f46513a704994bb297ed467012658ea3a8fea9549
	815a2c882d79dae039a4c30b748586ccc78be56eee03ed1832463ab5dfd15a8443898b2dff84971419a79ab242828fb1
	7440520fcdf7ba08cad0c9a8848c89fb77dec2be8b787f6bcf898de60f0aaf7c28e439770f89a7d447da5d4059b25e05
	7fa169d119fcf11e2e85be1fccc7e0e68498e2ce000db0b03f856e5445b3352c1d8c485d66d3f272b45cd30354e91633
	63478e2a64e74002dfd3c8cb7d3460b12918782242649645654afc4ed29226c1663d4d47feb80ff49f1a76b1d122dc51
	c0430a7111e2b7cea56f265678c2290e2e1b8649e3853d3db794b8b0610a22ae72b5271da010a111123aff55e34c4b25
	20d30b36a84c7664005d7fdb999302d85c4f3f9205ecb4eadc2626e89b4b020fc3cffa0ea154dd742b4c7db4394d72c9
)

source=(
	git+https://github.com/ghedo/http2-push-nginx-module
	git+https://github.com/google/ngx_brotli.git
	https://caddyserver.com/download/linux/amd64
	https://codeload.github.com/pagespeed/ngx_pagespeed/tar.gz/latest-beta
	https://nginx.org/download/nginx-$pkgver.tar.gz
	https://www.openssl.org/source/openssl-$opensslver.tar.gz
	logrotate
	nginx__204-headers.patch
	nginx__dynamic-tls-records.patch
	nginx__http2-hpack.patch
	nginx__http2-push.patch
	nginx__http2-spdy.patch
	nginx__server-header.patch
	openssl__equal-preference.patch
	pagespeed__nginx-unbreak.patch
	service
)

_build_parameters=(
	--add-module=../http2-push-nginx-module
	--add-module=../ngx_brotli
	--add-module=../ngx_pagespeed-latest-beta
	--conf-path=/etc/hws/nginx.conf
	--error-log-path=stderr
	--http-client-body-temp-path=/var/lib/hws/body
	--http-proxy-temp-path=/var/lib/hws/proxy
	--prefix=/etc/hws
	--sbin-path=/usr/bin/hws-nginx
	--with-cc-opt="-DTCP_FASTOPEN=23"
	--with-file-aio
	--with-http_realip_module
	--with-http_spdy_module
	--with-http_ssl_module
	--with-http_v2_hpack_enc
	--with-http_v2_module
	--with-openssl=../openssl-$opensslver
	--with-threads
	--without-http_access_module
	--without-http_auth_basic_module
	--without-http_autoindex_module
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
	cd ngx_brotli
	git submodule update --init

	cd ../ngx_pagespeed-latest-beta
	cat ../pagespeed__*.patch | patch -p1
	curl "$(scripts/format_binary_url.sh PSOL_BINARY_URL)" | tar xz

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

	install -dm700 -ghttp -ohttp "$pkgdir"/var/cache/hws "$pkgdir"/var/lib/hws/body "$pkgdir"/var/lib/hws/proxy "$pkgdir"/var/log/hws

	for i in contrib/vim/*; do
		install -Dm644 $i/nginx.vim "$pkgdir"/usr/share/vim/vimfiles/$i##*//nginx.vim
	done
}
