pkgname=hws
pkgdesc="Hydronium Web Server"

arch=(i686 x86_64)
conflicts=(caddy nginx)
depends=(pcre zlib)
makedepends=(git)
opensslver=1.1.0g
pkgrel=1
pkgver=1.13.6
provides=(caddy nginx)

sha384sums=(
	SKIP
	SKIP
	d4f5747f4edeb7f15cae8892007df10654ab2a892477fd9beea21aa4c1d18bd839bebcd64610ee9fbff85dcf4406b7ea
	b59c33399c1d1022c5c5b02ff21e17b4166c6255e02f52b01a7bc612ab5bd292db2b39bd944fd343ebb663ed5662e633
	57dcd7e38d6cfb1e9da88af3992da3754e5acdab8b898c32ca6c118b69e50f298b48f31a9fdfb5db1b39e54131a8e90d
	4d74cc598faacd64ec90a6d59691ace30bcee62220ee03a5a64901b2fe62e99fe861bb5f9fdc8bb16f7abcfe65815f58
	46a0bd7d28e7187eb138cc00dcd79caccbe42dba5ca19e07597403a52efb7557ed77944253c92c95a75da94f1856e1d7
	4194b218f3c4d60fd233fc02e8ec8ed3ffca4abb379e25f55786e93c653750d27cc38701aace956a055903da4e27b615
	bb23982291254485033925d59f5f5c9ac13481aa2fd1b619140109b2347eb215914a5c4ea51dd0824097cf997d0e2a4b
	f3a0377326f57a25343868bc1e2e4604753d90a62e1896a1316f3478e32fbce9c0c46cb57c8cd80527b3da95d868978b
	affa0695127b998a69a1468a5fac0d821b9589c7142887bfb2d9a3d3f491b1047a0befd2448b51ac3eabcc941ca31a76
	e210b79912e808041bdb4114db89b565f900131dac65360f3744399e971bbe7109609163cdc349e67f2d1e71a19395f2
	a4d6d919af239795e208057863542f2d44f1cb73be22f351727f1ba09d9def103583e8c925aa196a490c8905d69e10b9
	ddfe6eaa24776aec99bd6d15505d75667d3b1d74d0ac9ddde10f68e77cdda8e91f490019af5faf466afe13b61df7734f
	bdd5219cf7988392ef61958d65555ed0cde3f95948e240b010b0234ccd10b5a3ce66ff1c30d239ab204bfe41266279d7
	6b679a8f03b9930dc5ebb36a25f4f5d270d5a6118bfc447c77b1f2156ff7d8a25a916992295a98405276e687c321b00d
	b3c8965d0f7d0f0430a7dc1e023e42d7b25caf856db528ad86e1ee4e4ee8b738fb304abcf8ea44ecb0462b252701b03d
)

source=(
	git+https://github.com/ghedo/http2-push-nginx-module
	git+https://github.com/google/ngx_brotli.git
	https://caddyserver.com/download/linux/amd64?license=personal
	https://codeload.github.com/pagespeed/ngx_pagespeed/tar.gz/latest-beta
	https://nginx.org/download/nginx-$pkgver.tar.gz
	https://www.openssl.org/source/openssl-$opensslver.tar.gz
	logrotate
	nginx__204-headers.patch
	nginx__autoindex-epoch.patch
	nginx__autoindex-fts.patch
	nginx__dynamic-tls-records.patch
	nginx__http2-hpack.patch
	nginx__http2-push.patch
	nginx__http2-spdy.patch
	nginx__server-header.patch
	openssl__equal-preference.patch
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
	--with-http_addition_module
	--with-http_realip_module
	--with-http_spdy_module
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
	cd ngx_brotli
	git submodule update --init

	cd ../ngx_pagespeed-latest-beta
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

	for i in contrib/vim/*; do
		install -Dm644 $i/nginx.vim "$pkgdir"/usr/share/vim/vimfiles/$i##*//nginx.vim
	done
}
