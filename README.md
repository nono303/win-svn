# Apache Subversion Windows builds #  
## with httpd mod_X_svn and JavaHL ##   
These builds are based on original [Subversion source from github](https://github.com/apache/subversion).

----
**2019-02-12**

**compiled with:**  
- [subversion 1.11.1 tag](https://github.com/apache/subversion/tree/1.11.1)
- **[AVX](https://msdn.microsoft.com/fr-fr/library/jj620901.aspx) build** __only on specified directory__
- MSVC 15.9.6 / 19.16.27026.1
  - MSVC redist 14.16.27024 [x86](https://aka.ms/vs/15/release/VC_redist.x86.exe) - [x64](https://aka.ms/vs/15/release/VC_redist.x64.exe)
- Window Kit 10.0.17763.0
----
**openssl patch** - *see [#3](https://github.com/nono303/win-svn/issues/3#issuecomment-462668858)* - thanks @Jan-E  
Fix is on openssl header so **you don't need any patched deps**. Refs: 
- https://github.com/openssl/openssl/commit/ef45aa14c5af024fcb8bef1c9007f3d1c115bd85
- https://github.com/openssl/openssl/issues/2865
- https://bz.apache.org/bugzilla/show_bug.cgi?id=63139
- https://github.com/apache/apr-util/pull/8
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

## Install On Apahce httpd

1. copy *mod_dav_svn.so*, *mod_authz_svn.so*, **and all *.dll* files under */deps*** to Apache httpd *modules/* folder
2. load the modules by adding following lines in apache config

    ```
    LoadModule dav_svn_module modules/mod_dav_svn.so
    LoadModule authz_svn_module modules/mod_authz_svn.so
    ```
3.  add svn directives to  apache config. If using [mod_authn_ntlm](https://github.com/TQsoft-GmbH/mod_authn_ntlm) for authentication, the directives will look like

    ```
    <Location /svn>
        NTLMAuth on
        NTLMUsernameCase  lower
        NTLMOfferBasic On
        DAV svn
        SVNPath "c:\svn_repo"
        SVNReposName "My Subversion Repository"
        AuthzSVNAccessFile "c:\svn_repo\conf\authz"
        Require valid-user
    </Location>
    ```
