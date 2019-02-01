# Apache Subversion Windows builds #  
## with httpd mod_X_svn and JavaHL ##   
These builds are based on original [Subversion source from github](https://github.com/apache/subversion).

----
**2019-02-01**

- [subversion 1.11.1 tag](https://github.com/apache/subversion/tree/1.11.1)
- MSVC 15.9.6 / 19.16.27026.1
  - MSVC15 redist 14.16.27024 [x86](https://aka.ms/vs/15/release/VC_redist.x86.exe) - [x64](https://aka.ms/vs/15/release/VC_redist.x64.exe)
- Window Kit 10.0.17763.0
- **[AVX](https://msdn.microsoft.com/fr-fr/library/jj620901.aspx) build** __only on specified directory__
----
**apr_crypto_openssl-1.dll patched** according to [openssl issue 2865](https://github.com/openssl/openssl/issues/2865) *(also see #3)*
```
apr_crypto_openssl.c

@@ -132,8 +132,8 @@ static apr_status_t crypto_init(apr_pool_t *pool, const char *params,
 {
 #if APR_USE_OPENSSL_PRE_1_1_API
     (void)CRYPTO_malloc_init();
-#else
-    OPENSSL_malloc_init();
+// #else
+//     OPENSSL_malloc_init();
```
  - keep `apr_crypto_openssl-1.dll` in svn exe root path because I don't know consequences (ie. for httpd) disabling `OPENSSL_malloc_init()`...
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