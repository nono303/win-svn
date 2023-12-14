# Apache Subversion - Windows MSVC binaries #
#### Including Apache httpd `mod_X_svn` and `JavaHL Native Library` Adapter   
 - https://github.com/apache/subversion

----
### Version [1.14.3](https://github.com/apache/subversion/tree/1.14.3)
> 2022-12-09 - commit
>
> 2022-12-14 - build
- **VS17** toolset: 14.39.33218
- **VS16** toolset: 14.29.30133
- **VC15** toolset: 14.16.27023
- MSVC redist:  [x86](https://aka.ms/vs/16/release/vc_redist.x86.exe) - [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)
- Window Kit: 10.0.22621.0
- **[AVX](https://msdn.microsoft.com/fr-fr/library/jj620901.aspx) releases** _in specified directory_

[**How to get the good version**](https://github.com/nono303/PHP-memcache-dll#how-to-get-the-good-version)

**Build Scripts** 

- [@nono303/win-build-scripts](https://github.com/nono303/win-build-scripts)
- cflags: `/O2 /GL /MD /Zi`
- ldflags: ` /LTCG /OPT:REF,ICF`

**Build  & Runtime Dependencies**

> * All dependencies are built from sources in the same context

 - [openssl 3.1.4](https://github.com/openssl/openssl/tree/openssl-3.1.4) 
 - [apr 1.8.0-dev](https://github.com/apache/apr)
 - [apr-util 1.7.0-dev](https://github.com/apache/apr-util)
    - *apr_memcache 1.7.0-dev*
 - [libexpat 2.5.0](https://github.com/libexpat/libexpat/tree/R_2_5_0)
 - [httpd 2.4.58](https://github.com/apache/httpd/tree/2.4.58) 
 - [serf 2.0.0-dev](https://github.com/apache/serf/commit/c8b756c192658807a9219e2b58d6234a2691501a)
 - [sqlite 3.44.2](https://github.com/rhuijben/sqlite-amalgamation/tree/3.44.2)
 - [zlib 1.3](https://github.com/madler/zlib/tree/v1.3)
 - lz4 1.7.5 *(bundled)*
 - utf8proc 2.1.0 *(bundled)*
 - [OpenJDK 21.0.1](https://jdk.java.net/21/)

**Runtime Dependencies**

> Provided dll dependencies in `/deps` allow you to run subversion <u>autonomously</u> but depending to your runtime environment, all of them might **NOT** be used to run subversion - **It can cause conflicts!**
> ex. in case of an httpd module usage, most of them might already be present in the `/httpd/bin` folder.
>
> Depending on your httpd distribution & version and how you manage your `PATH` environment _(with or without `/httpd/bin` in)_ you might test - in case of trouble with a [Dependency Walker](https://github.com/lucasg/Dependencies) or `dumpbin /DEPENDENTS` - **which provided dll dependencies are required in your particular context**.
>
> Note that `/httpd/bin` dll dependencies are not built in the same way between standard distributions ([Apache Lounge](https://www.apachelounge.com/), [Apache Haus](https://www.apachehaus.com/), [WampServer](https://www.wampserver.com/), [XAMPP](https://www.apachefriends.org/download.html)) and mine _(configure & compile options, flags, etc.)_ and moreover, they may not be in the same version!
>
> So... I don't have a good and unique answer on how manage this but in case of conflict, my advice would be:
>
> - :white_check_mark: [hardlink](https://docs.microsoft.com/en-us/windows/win32/fileio/hard-links-and-junctions) needed provided dll dependencies  in `/deps` to your subversion root folder
> - :warning: **NOT** changing PATH environment 
> - **:no_entry_sign: DO NOT RENAME FILES** In any case
> 
> Note that `libhttpd.dll`  - _only required by the 3 `mod_xxx.so`_ - is not provided as httpd modules will be launch in **your httpd runtime context containing your own `libhttpd.dll` **

- **openssl** - `libcrypto-3-x64.dll libssl-3-x64.dll`
- **apr** - `libapr-1.dll`  `libaprutil-1.dll` `libapriconv-1.dll`
- **expat** - `libexpat.dll`
  - :warning: _see [this topic](https://www.apachelounge.com/viewtopic.php?p=38610#38610) concerning naming_
    - *see [this topic](https://www.apachelounge.com/viewtopic.php?p=38610#38610)  if you already have `expat.dll`*
  - *mandatory for svn as module in httpd standard distributions*, see [#6](https://github.com/nono303/win-svn/issues/6#issuecomment-677525851)
- **serf** - `libserf-2.dll`
- **sqlite3** - `libsqlite3.dll`
  - :warning: _see [this topic](https://github.com/nono303/win-build-scripts/issues/2#issuecomment-1855537078) concerning naming_
    - _avoid conflict with sqlite3 shell with shared `sqlite3.dll` as it require a specific compilation flag for subversion and no dependency to **ICU**_
- **zlib** - `zlib.dll`
  - :warning: _see [this patch](https://github.com/winlibs/zlib/blob/master/winlibs.patch) concerning naming_

### Install on Apache httpd  
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

### mod_dotndothat config
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