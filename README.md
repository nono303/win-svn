# Apache Subversion - Windows MSVC binaries #
### including Apache httpd mod_X_svn and JavaHL Native Library Adapter   
 - https://github.com/apache/subversion.

----
2019-02-27
> version [1.11.1](https://github.com/apache/subversion/tree/1.11.1)

- MSVC 15.9.7 / 19.16.27027.1
  - MSVC redist 14.16.27024.1 [x86](https://aka.ms/vs/15/release/VC_redist.x86.exe) - [x64](https://aka.ms/vs/15/release/VC_redist.x64.exe)
- Window Kit 10.0.17763.0
- **[AVX](https://msdn.microsoft.com/fr-fr/library/jj620901.aspx) releases** __for specified directory__

**Build Dependencies**  
*All dependencies, excluding java, are built from sources in the same context*
 - [openssl 1.1.1b](https://github.com/openssl/openssl/tree/OpenSSL_1_1_1b) - __crypto header [patched](https://github.com/openssl/openssl/commit/ef45aa14c5af024fcb8bef1c9007f3d1c115bd85) for [#2865](https://github.com/openssl/openssl/issues/2865)__
   - see [#3](https://github.com/nono303/win-svn/issues/3#issuecomment-462668858) - thanks @Jan-E. Refs: 
      - https://github.com/openssl/openssl/commit/ef45aa14c5af024fcb8bef1c9007f3d1c115bd85
      - https://github.com/openssl/openssl/issues/2865
      - https://bz.apache.org/bugzilla/show_bug.cgi?id=63139
      - https://github.com/apache/apr-util/pull/8
    - verify patch executing `crypto-test.exe` result must be:
      ```
      PASS:  crypto-test 1: basic password encryption/decryption test
      PASS:  crypto-test 2: password checktext generation/validation
      ```
 - apr 1.6.5
 - apr-util 1.6.1
 - apr_memcache 1.6.1
 - brotli 1.0.7
 - expat 2.2.6
 - httpd & mod_dav 2.4.38
 - serf 1.4.0
 - sqlite 3.27.1
 - zlib 1.2.11 ASM build
 - lz4 1.7.5 *(bundled)*
 - utf8proc 2.1.0 *(bundled)*
 - java-sdk 1.8.0_201 *(Oracle)*

**Exec Dependencies**

- `/deps` with dll and pdb for:
  - openssl
  - brotli
  - serf
  - aprutil
  - apriconv
  - apr
  - expat

## Install on Apache httpd  
#### [@nono303](https://github.com/nono303) method  
*easier to upgrade & httpd independant*  
1. Add `/win-svn/vc15/(x64|x86)` and `/win-svn/vc15/(x64|x86)/deps` to *PATH* environment variable, after your `/httpd/bin` entry.
2. Load the modules needed by adding following lines, in httpd config, with **absolute path**:

    ```
    LoadModule dav_svn_module "C:/.../win-svn/vc15/(x64|x86)/mod_dav_svn.so"
    LoadModule authz_svn_module "C:/.../win-svn/vc15/(x64|x86)/mod_authz_svn.so"
    ```

#### [@f-w](https://github.com/f-w]) method
*need copy for upgrade, httpd integrated*
1. Add `win-svn/vc15/(x64|x86)` to *PATH* environment variable.
2. Copy `.so` and **all `.dll` files under `win-svn/vc15/(x64|x86)/deps`** to `/httpd/modules` folder
3. Load the modules needed by adding following lines, in httpd config:

    ```
    LoadModule dav_svn_module modules/mod_dav_svn.so
    LoadModule authz_svn_module modules/mod_authz_svn.so
    ```

----
#### Finally 
Add svn directives to httpd config. 
If using [mod_authn_ntlm](https://github.com/TQsoft-GmbH/mod_authn_ntlm) for authentication, the directives will look like:

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