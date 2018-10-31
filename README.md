# Apache Subversion Windows builds #
## with httpd mod_X_svn and JavaHL ##

----
These builds are based on original [Subversion source from github](https://github.com/apache/subversion).

----
**2018-10-31**

- [subversion 1.11.0 tag](https://github.com/apache/subversion/tree/1.11.0)
- MSVC 15.8.8 / 19.15.26732.1
- Window Kit 10.0.17134.0

**Build Dependencies**  
 ``All dependencies, excluding java, are built from sources in the same context``

 - apr 1.6.5
 - apr-util 1.6.1
 - apr_memcache 1.6.1
 - brotli 1.0.7
 - expat 2.2.6
 - httpd & mod_dav 2.4.37
 - java-sdk 1.8.0_181 (Oracle)
 - openssl 1.1.1
 - serf 1.4.0
 - sqlite 3.25.2
 - zlib 1.2.11 ASM build
 - lz4 1.7.5 (bundled)
 - utf8proc 2.1.0 (bundled)

**Run Dependencies**

- MSVC15 redist 14.15.26706 [x86](https://aka.ms/vs/15/release/VC_redist.x86.exe) - [x64](https://aka.ms/vs/15/release/VC_redist.x64.exe)
- /deps with dll and pdb for:
  - openssl
  - brotli
  - serf
  - aprutil
  - apriconv
  - apr
  - expat