# Apache Subversion Windows builds #  
## with httpd mod_X_svn and JavaHL ##   
These builds are based on original [Subversion source from github](https://github.com/apache/subversion).

----
**2019-02-02**

**compiled with:**  
- [subversion 1.11.1 tag](https://github.com/apache/subversion/tree/1.11.1)
- **[AVX](https://msdn.microsoft.com/fr-fr/library/jj620901.aspx) build** __only on specified directory__
- MSVC 15.9.6 / 19.16.27026.1
  - MSVC redist 14.16.27024 [x86](https://aka.ms/vs/15/release/VC_redist.x86.exe) - [x64](https://aka.ms/vs/15/release/VC_redist.x64.exe)
- Window Kit 10.0.17763.0
----
**apr_crypto_openssl-1.dll patched** according to [openssl issue 2865](https://github.com/openssl/openssl/issues/2865) & thanks to @Jan-E - *see [#3](https://github.com/nono303/win-svn/issues/3#issuecomment-459882588)*
```
--- crypto/apr_crypto_openssl.c.orig	2017-10-08 13:32:30.000000000 +0200
+++ crypto/apr_crypto_openssl.c	2019-02-01 23:04:13.083613500 +0100
@@ -30,6 +30,7 @@
 
 #if APU_HAVE_CRYPTO
 
+#include <openssl/ssl.h>
 #include <openssl/evp.h>
 #include <openssl/rand.h>
 #include <openssl/engine.h>
@@ -133,6 +134,8 @@
 #if APR_USE_OPENSSL_PRE_1_1_API
     (void)CRYPTO_malloc_init();
 #else
+    /* SSL_library_init() must be called prior to any other OpenSSL action. */
+    SSL_library_init();
     OPENSSL_malloc_init();
 #endif
     ERR_load_crypto_strings();
```
  - keep `apr_crypto_openssl-1.dll` in svn exe root path because I didn't yet qualify consequences for other applications (ie. for httpd)...
  - verify patch executing `crypto-test.exe` result must be:
```
PASS:  crypto-test 1: basic password encryption/decryption test
PASS:  crypto-test 2: password checktext generation/validation
```
----
**Build Dependencies**  
``All dependencies, excluding java, are built from sources in the same context``

 - apr 1.6.5
 - apr-util 1.6.1
 - apr_memcache 1.6.1
 - brotli 1.0.7
 - expat 2.2.6
 - httpd & mod_dav 2.4.38
 - java-sdk 1.8.0_201 (Oracle)
 - openssl 1.1.1a
 - serf 1.4.0
 - sqlite 3.26.0
 - zlib 1.2.11 ASM build
 - lz4 1.7.5 (bundled)
 - utf8proc 2.1.0 (bundled)

**Run Dependencies**

- /deps with dll and pdb for:
  - openssl
  - brotli
  - serf
  - aprutil
  - apriconv
  - apr
  - expat