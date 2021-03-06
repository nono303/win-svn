# Apache Subversion - Windows MSVC binaries #
### Including Apache httpd `mod_X_svn` and `JavaHL Native Library` Adapter   
 - https://github.com/apache/subversion

----
## `VC15 & VS16`

- VS16 : toolset 14.28.29333
- VC15 : toolset 14.16.27023
  - MSVC redist  [x86](https://aka.ms/vs/16/release/vc_redist.x86.exe) - [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)
- Window Kit 10.0.19041.0
- **[AVX](https://msdn.microsoft.com/fr-fr/library/jj620901.aspx) releases** __for specified directory

## Version [1.14.1](https://github.com/apache/subversion/tree/1.14.1)
> 2021-02-05
- **Build Scripts** 
- [@nono303/win-build-scripts](https://github.com/nono303/win-build-scripts)
- cflags: `/O2 /GL /MD /Zi`
- ldflags: `/LTCG /OPT:ICF`

**Build Dependencies**  
*All dependencies are built from sources in the same context*

 - openssl 1.1.1i
 - apr 1.7.0
 - apr-util 1.6.1
 - apr_memcache 1.6.1
 - libexpat 2.2.10
 - httpd & mod_dav 2.4.46
 - serf 2.0.0
 - sqlite 3.34.1
 - zlib 1.2.11 ASM build
 - lz4 1.7.5 *(bundled)*
 - utf8proc 2.1.0 *(bundled)*
 - OpenJDK 15.0.2

**Runtime Dependencies**

> Depending to your runtime environment, all dependencies might **NOT** be used to run subversion!
>
> Provided files allow you to run subversion <u>autonomously</u>. 
> But in the case of an httpd module usage, most of these dependencies may already be present in the /httpd/bin folder.
>
> So, depending on your httpd distribution / version and how you manage your PATH environment var (with or without /httpd/bin in) you might test, in case of trouble, with a [Dependency Walker](https://github.com/lucasg/Dependencies) which dll are required in your particular context.
>
> Also not that /httpd/bin dependencies are not built in the same way between standard distributions ([Apache Lounge](https://www.apachelounge.com/), [Apache Haus](https://www.apachehaus.com/), [WampServer](https://www.wampserver.com/), [XAMPP](http://www.apachefriends.org/en/xampp.html), [BitNami WAMP](http://bitnami.com/stack/wamp)) and mine (and moreover may not be in the same version...)
>
> I don't have an absolute and good answer on how manage this but in case of conflict, my advice would be to **NOT** use PATH environment and [hardlink](https://docs.microsoft.com/en-us/windows/win32/fileio/hard-links-and-junctions) needed dll in /deps to your subversion root folder

- `/deps` with dll and pdb for:
  - libexpat ![https://www.apachelounge.com/viewtopic.php?p=38610#38610](https://placehold.it/15/f03c15/000000?text=+) 
    - *mandatory for svn as module in httpd standard distributions*, see [#6](https://github.com/nono303/win-svn/issues/6#issuecomment-677525851)
    - *see [this topic](https://www.apachelounge.com/viewtopic.php?p=38610#38610)  if you already have `expat.dll`*
  - openssl
  - zlib
  - brotli
  - serf
  - aprutil
  - apriconv
  - apr

## Install on Apache httpd  
#### [@nono303](https://github.com/nono303) method  
*easier to upgrade & httpd independent*

1. 

    - Add your  `/deps` directory name to Windows *PATH* environment variable, after your `/httpd/bin` entry, if setted 

        **OR** 

    - just hardlink from`/deps` to `/` dll that are NOT present in your `/httpd/bin` folder (to avoid duplicate and/or version conflicts) 

        - ex. `mklink /h C:\XXX\win-svn\vs16\x64-avx\libexpat.dll C:\XXX\win-svn\vs16\x64-avx\deps\libexpat.dll`

2. Load the modules needed by adding following lines, in httpd config, with **absolute path**:

    ```
    LoadModule dav_module modules/mod_dav.so # included in httpd distribution
    ...
    LoadModule dav_svn_module "C:/.../win-svn/vc15/(x64|x86)/mod_dav_svn.so"
    LoadModule authz_svn_module "C:/.../win-svn/vc15/(x64|x86)/mod_authz_svn.so"
    ```

#### [@f-w](https://github.com/f-w]) method
*need copy for upgrade, httpd integrated*
1. Add `win-svn/vc15/(x64|x86)` to *PATH* environment variable.
2. Copy `.so` and **all `.dll` files under `win-svn/vc15/(x64|x86)/deps`** to `/httpd/modules` folder
3. Load the modules needed by adding following lines, in httpd config:

    ```
    LoadModule dav_module modules/mod_dav.so # included in httpd distribution
    ...
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