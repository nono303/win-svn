# Apache Subversion Windows builds #
## with httpd mod_X_svn and JavaHL ##

----
These builds are based on original [Subversion source from github](https://github.com/apache/subversion).

----
**2018-07-24**

- [subversion 1.10.2 tag](https://github.com/apache/subversion/tree/1.10.2)
- MSVC 15.7.5 / 19.14.26433
- Window Kit 10.0.17134.0

**Build Dependencies**  
 ``All dependencies, excluding java, are built from sources in the same context``

 - apr 1.6.3
 - apr-util 1.6.1
 - apr_memcache 1.6.1
 - brotli 1.0.5
 - expat 2.2.5
 - httpd & mod_dav 2.4.33
 - java-sdk 1.8.0_181 (Oracle)
 - openssl 1.1.0h
 - serf 2.0.0
 - sqlite 3.24.0
 - zlib 1.2.11
 - lz4 1.7.5 (bundled)
 - utf8proc 2.1.0 (bundled)

**Run Dependencies**

- MSVC15 redist 14.14.26429 [x86](https://aka.ms/vs/15/release/VC_redist.x86.exe) - [x64](https://aka.ms/vs/15/release/VC_redist.x64.exe)
- /deps with dll and pdb for:
  - openssl
  - brotli
  - serf
  - aprutil
  - apriconv
  - apr
  - expat