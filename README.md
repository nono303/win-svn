# Apache Subversion Windows builds #
## with httpd mod_X_svn and JavaHL ##

----
These builds are based on original [Subversion source from github](https://github.com/apache/subversion).

----
**2018-03-28**

- [subversion 1.10.0-rc1 tag](https://github.com/apache/subversion/tree/1.10.0-rc1)
- MSVC 15.6.4 / 19.13.26129
- Window Kit 10.0.16299.0

**Build Dependencies**  
 ``All dependencies, excluding java, are built from sources in the same context``

 - apr 1.6.3
 - apr-util 1.6.1
 - apr_memcache 1.6.1
 - brotli 1.0.3
 - expat 2.2.5
 - httpd & mod_dav 2.4.33
 - java-sdk 1.8.0_162 (Oracle)
 - openssl 1.1.0h
 - serf 2.0.0
 - sqlite 3.22.0
 - zlib 1.2.11
 - lz4 1.7.5
 - utf8proc 2.1.0

**Run Dependencies**

- MSVC15 redist 14.13.26020 [x86](https://aka.ms/vs/15/release/VC_redist.x86.exe) - [x64](https://aka.ms/vs/15/release/VC_redist.x64.exe)
