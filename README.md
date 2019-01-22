# Apache Subversion Windows builds #
## with httpd mod_X_svn and JavaHL ##

----
These builds are based on original [Subversion source from github](https://github.com/apache/subversion).

----
**2019-11-22**

- [subversion 1.11.1 tag](https://github.com/apache/subversion/tree/1.11.1)
- MSVC 15.9.5 / 19.16.27026.1
- Window Kit 10.0.17763.0

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

- MSVC15 redist 14.16.27024 [x86](https://aka.ms/vs/15/release/VC_redist.x86.exe) - [x64](https://aka.ms/vs/15/release/VC_redist.x64.exe)
- /deps with dll and pdb for:
  - openssl
  - brotli
  - serf
  - aprutil
  - apriconv
  - apr
  - expat