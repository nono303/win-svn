# Apache Subversion - Windows MSVC binaries #
### including Apache httpd `mod_X_svn` and `JavaHL Native Library` Adapter   
 - https://github.com/apache/subversion

----
## Version [1.13.0](https://github.com/apache/subversion/tree/1.13.0)
> 2019-12-20
- Visual Studio 2019 v16.4.2
- VS16 : toolset 14.24.28314
- VC15 : toolset 14.16.27023
- Window Kit 10.1.18362.1
----
- **[AVX](https://msdn.microsoft.com/fr-fr/library/jj620901.aspx) releases** __for specified directory__
- MSVC redist  [x86](https://aka.ms/vs/16/release/vc_redist.x86.exe) - [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

**Build Dependencies**  
*All dependencies are built from sources in the same context*
 - openssl 1.1.1d
 - apr 1.7.0
 - apr-util 1.6.1
 - apr_memcache 1.6.1
 - libexpat 2.2.9
 - httpd & mod_dav 2.4.41
 - serf 2.0.0
 - sqlite 3.30.1
 - zlib 1.2.11 ASM build
 - lz4 1.7.5 *(bundled)*
 - utf8proc 2.1.0 *(bundled)*
 - java-sdk 1.8.0_231 *(Oracle)*

**Exec Dependencies**

- `/deps` with dll and pdb for:
  - openssl
  - brotli
  - serf
  - aprutil
  - apriconv
  - apr
  - libexpat ![https://www.apachelounge.com/viewtopic.php?p=38610#38610](https://placehold.it/15/f03c15/000000?text=+) see [this topic](https://www.apachelounge.com/viewtopic.php?p=38610#38610) concerning lib naming

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

## mod_dotndothat config
	mod_dontdothat is an Apache module that allows you to block specific types
	of Subversion requests.  Specifically, it's designed to keep users from doing
	things that are particularly hard on the server, like checking out the root
	of the tree, or the tags or branches directories.  It works by sticking an
	input filter in front of all REPORT requests and looking for dangerous types
	of requests.  If it finds any, it returns a 403 Forbidden error.
	
	It is enabled via single httpd.conf directive, DontDoThatConfigFile:
```xml
<Location /svn>
	DAV svn
	SVNParentPath /path/to/repositories
	DontDoThatConfigFile /path/to/config.file
	DontDoThatDisallowReplay off
</Location>
```
	The file you give to DontDoThatConfigFile is a Subversion configuration file
	that contains the following sections.

```ini
[recursive-actions]
/*/trunk = allow
/ = deny
/* = deny
/*/tags = deny
/*/branches = deny
/*/* = deny
/*/*/tags = deny
/*/*/branches = deny
```
	As you might guess, this defines a set of patterns that control what the
	user is not allowed to do.  Anything with a 'deny' after it is denied, and
	as a fallback mechanism anything with an 'allow' after it is special cased
	to be allowed, even if it matches something that is denied.
	
	Note that the wildcard portions of a rule only swallow a single directory,
	so /* will match /foo, but not /foo/bar.  They also must be at the end of
	a directory segment, so /foo* or /* are valid, but /*foo is not.
	
	These rules are applied to any recursive action, which basically means any
	Subversion command that goes through the update-report, like update, diff,
	checkout, merge, etc.
	
	The DontDoThatDisallowReplay option makes mod_dontdothat disallow
	replay requests, which is on by default.
	
	For packaging you might take a look at https://gist.github.com/JBlond/454a34095ed4c46aac24b3ce7e211ab3