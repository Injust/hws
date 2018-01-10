pkgname=hws
pkgdesc="Hydronium Web Server"

arch=(i686 x86_64)
conflicts=(caddy nginx)
depends=(pcre zlib)
makedepends=(git)
opensslver=1.1.0g
pkgrel=1
pkgver=1.13.8
provides=(caddy nginx)

sha384sums=(
	SKIP
	SKIP
	9ebf7f2f381c6e6c534962e427bb77a4f54e8017b3eb4dce42a36bf1599cf3fe54867d0bbca308fff34ff86a13a43dba
	0f62be93959eb5df320c9018a0bac69c6f8a167bf838f303751190be10a06384ae10d5726e1fbdec448f1c209d6197c7
	b504858449fc160b0a2ff1e30e0f226c36c0cd357a647e2cd7da4f43718c79fa3732e130d7d2e337ee2368a03516b5e7
	4d74cc598faacd64ec90a6d59691ace30bcee62220ee03a5a64901b2fe62e99fe861bb5f9fdc8bb16f7abcfe65815f58
	46a0bd7d28e7187eb138cc00dcd79caccbe42dba5ca19e07597403a52efb7557ed77944253c92c95a75da94f1856e1d7
	4194b218f3c4d60fd233fc02e8ec8ed3ffca4abb379e25f55786e93c653750d27cc38701aace956a055903da4e27b615
	9f775e455bc6adf1d83dddb4fc90ce935f772dd0ddf81e5dfab9d4af53aa395831d810a1aa2590f0dfe3ac979dd03639
	bdcabfa111bb89e5a64376c05e27c0752602b321796a4e7b221d64694b502a0b3cd65d82fae73381f8112cc5332c0244
	d66c9b53d50a1cb5a6ef43202f5e14c25998bc4d47fb460be11e2021bb5271bf07f5f473d51918ed7c009478a13704fd
	e210b79912e808041bdb4114db89b565f900131dac65360f3744399e971bbe7109609163cdc349e67f2d1e71a19395f2
	a4d6d919af239795e208057863542f2d44f1cb73be22f351727f1ba09d9def103583e8c925aa196a490c8905d69e10b9
	d377e267452881943c6a0d78a74ee674b150f0871098b54add0ca7ab77076504b5c7be766f1bceb4bfdcfbdcba844467
	bdd5219cf7988392ef61958d65555ed0cde3f95948e240b010b0234ccd10b5a3ce66ff1c30d239ab204bfe41266279d7
	6b679a8f03b9930dc5ebb36a25f4f5d270d5a6118bfc447c77b1f2156ff7d8a25a916992295a98405276e687c321b00d
	b3c8965d0f7d0f0430a7dc1e023e42d7b25caf856db528ad86e1ee4e4ee8b738fb304abcf8ea44ecb0462b252701b03d
)

source=(
	git+https://github.com/ghedo/http2-push-nginx-module
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
	nginx__http2-push.patch
	nginx__http2-spdy.patch
	nginx__server-header.patch
	openssl__equal-preference.patch
	service
)

_build_parameters=(
	--add-module=../http2-push-nginx-module
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
