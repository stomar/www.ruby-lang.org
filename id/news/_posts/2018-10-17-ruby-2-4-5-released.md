---
layout: news_post
title: "Ruby 2.4.5 Rilis"
author: "usa"
translator: "meisyal"
date: 2018-10-17 17:00:00 +0000
lang: id
---

Ruby 2.4.5 telah dirilis.

Rilis ini mencakup sekitar 40 perbaikan *bug* dari rilis sebelumnya
dan beberapa perbaikan keamanan.
Mohon cek topik-topik di bawah ini untuk lebih detail.

* [CVE-2018-16396: Penanda tercemar tidak disebarkan ke Array#pack dan String#unpack dengan beberapa arahan](/id/news/2018/10/17/not-propagated-taint-flag-in-some-formats-of-pack-cve-2018-16396/)
* [CVE-2018-16395: Pemeriksaan kesetaraan OpenSSL::X509::Name tidak berfungsi dengan benar](/id/news/2018/10/17/openssl-x509-name-equality-check-does-not-work-correctly-cve-2018-16395/)

Lihat [commit logs](https://github.com/ruby/ruby/compare/v2_4_4...v2_4_5) untuk detail.

## Unduh

* [https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.5.tar.bz2](https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.5.tar.bz2)

      SIZE:   12667524 bytes
      SHA1:   0e1f184556507c22bc59054496c0af7cf28fb188
      SHA256: 276c8e73e51e4ba6a0fe81fb92669734e741ccea86f01c45e99f2c7ef7bcd1e3
      SHA512: 7034fcaeaee41f14bc0ecce0d3d93bd1abe95310e1a0b95fac66eaba867adfb2bf7ba4d0d70d67a15ce8df16052dee405c38cdb18987602e64a2f701d37d3df0

* [https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.5.tar.gz](https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.5.tar.gz)

      SIZE:   14232887 bytes
      SHA1:   4d650f302f1ec00256450b112bb023644b6ab6dd
      SHA256: 6737741ae6ffa61174c8a3dcdd8ba92bc38827827ab1d7ea1ec78bc3cefc5198
      SHA512: 39863b404b02bb3d2430fd19fb057bdded5e816842a1a1f00092de69cc360db836225b2186e18b6ee099ec5e8c60f89178a4297b2d221234b4962c70594c2b8e

* [https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.5.tar.xz](https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.5.tar.xz)

      SIZE:   10064712 bytes
      SHA1:   b5be590b37487248da3a85541a62fb81f7f7e29a
      SHA256: 2f0cdcce9989f63ef7c2939bdb17b1ef244c4f384d85b8531d60e73d8cc31eeb
      SHA512: 658f676c623109f4c7499615e191c98c3dd72cfcaeeaf121337d0b8a33c5243145edd50ec5e2775f988e3cd19788984f105fa165e3049779066566f67172c1b4

* [https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.5.zip](https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.5.zip)

      SIZE:   15694028 bytes
      SHA1:   0bded7dfabfce5615162a1acd1341966a63e40f4
      SHA256: 33694b03ac178cf96aa728b74de7b0bc5d848fcfabc64a7c74ea093198142601
      SHA512: 9baec2b33604d5b2180b83bef9b39349d3f73b2eb5a7d44410572d893f76e6a0e1c39c572e6084a4e2466ca148ff2f377b5458144fe9b859a80cdf7b41fd5d72

## Komentar Rilis

Terima kasih kepada semua yang telah membantu rilis ini.

Perawatan Ruby 2.4, termasuk rilis ini, didasarkan pada
"Agreement for the Ruby stable version" dari Ruby Association.
