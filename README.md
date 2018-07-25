# Configuration

Support for setting up your development machine on the LM network.  Note that the following README assumes that the user downloads lm_combined_certs.cer and places that file in the user's home folder which can be found with the environment variable %userprofile%.  On windows this is likely c:/Users/{user}, and can be found by executing "echo %userprofile%" at the command prompt.  Alternatively the batch file in this repo will take care of all of this automatically. If the user does not need all of the tools, he/she can simply comment out the ones that are not needed and run it without them.

## References:

PKI team trust store index: https://eo-sharepoint-restricted-m.external.lmco.com/sites/AssuredIdentity/team/LAI/ePKI/Wiki/PKI%20team%20trust%20store%20index.aspx

Combined trust store (LM Chain + Approved 3rd Party Roots):  https://eo-sharepoint-restricted-m.external.lmco.com/sites/AssuredIdentity/team/LAI/ePKI/docreview/Trust%20Architecture%20Project/Shared%20Documents/Forms/AllItems.aspx?RootFolder=/sites/AssuredIdentity/team/LAI/ePKI/docreview/Trust+Architecture+Project/Shared+Documents/PKI+Trust+Architecture+Key+Stores/Base64+Certificates/Combined&InitialTabId=Ribbon.Document&VisibilityContext=WSSTabPersistence

Latest combined repo as of 2017/12/21:  https://eo-sharepoint-restricted-m.external.lmco.com/sites/AssuredIdentity/team/LAI/ePKI/docreview/Trust%20Architecture%20Project/Shared%20Documents/PKI%20Trust%20Architecture%20Key%20Stores/Base64%20Certificates/Combined/Combined20171010_b64.zip


## proxy

```
:: proxy config

:: set(x) the http proxy to lm proxy
set HTTP_PROXY=http://proxy-lmi.global.lmco.com:80 > nul
setx HTTP_PROXY http://proxy-lmi.global.lmco.com:80 > nul

:: set(x) the https proxy to lm proxy
set HTTPS_PROXY=http://proxy-lmi.global.lmco.com:80 > nul
setx HTTPS_PROXY http://proxy-lmi.global.lmco.com:80 > nul

:: set(x) no proxy
::  bitbucket - susrnf01.sikorsky.utccommercial.us
::  nuget server - susrnk04.sikorsky.utccommercial.us
::  ASHM DAT dev server - susrnk04.sikorsky.utccommercial.us
::           staging - xmnt101i.utc.com
::           prod - xnwp563i.utc.com
set NO_PROXY=susrnf01.sikorsky.utccommercial.us,susrnk04.sikorsky.utccommercial.us,xmnt101i.utc.com,xnwp563i.utc.com,gitlab.lmms.lmco.com > nul
setx NO_PROXY susrnf01.sikorsky.utccommercial.us,susrnk04.sikorsky.utccommercial.us,xmnt101i.utc.com,xnwp563i.utc.com,gitlab.lmms.lmco.com > nul
```

## git

Update the git configuration file in your users home folder called **.gitconfig** with the following contents:

```
[http]
     sslverify = true
     sslCAInfo = C:/Users/{user}/lm_combined_certs.cer
     credential.helper = wincred
```

Or use the script:

```
:: git config

:: set git to use ssl
git config --global http.sslverify true

:: configure git to use the cert as a CAs
git config --global http.sslCAInfo %userprofile%\lm_combined_certs.cer

:: configure git to use schannel for sslbackend
git config --global http.sslbackend schannel

:: configure git to use wincred
git config --global credential.helper wincred
```

## conda

In explorer, go to your users home folder:  C:/Users/{user}
You have to create a file called “.condarc” – if you try this in explorer Windows will tell you it is an invalid file name, so you’ll have to use DOS.  Shift-right-click in that window, “open command window here”.  At the prompt type “copy NUL .condarc” to create that file.  Open the file in a text editor and put the following, making sure that you put the correct full path to your local LM root certificate file using forward slashes for folder separators:

```
proxy_servers:
    http: proxy-lmi.global.lmco.com:80
    https: proxy-lmi.global.lmco.com:80
ssl_verify: "C:/Users/{user}/lm_combined_certs.cer"
```

Or use the script:

```
:: conda config

:: configure conda to use lm root CA
@echo proxy_servers: > %userprofile%\.condarc
@echo     http: proxy-lmi.global.lmco.com:80 >> %userprofile%\.condarc
@echo     https: proxy-lmi.global.lmco.com:80 >> %userprofile%\.condarc
@echo ssl_verify: "%usershomeunix%/lm_combined_certs.cer" >> %userprofile%\.condarc
```

## npm

```
:: npm config

:: configure npm to use lm root CA
:: NOTE: other option would be to use the following commands (but why require npm to run this script, that is too node centric)
::  @ echo %usershomeunix%/lm_combined_certs.cer > .npmrc
::  npm config set cafile %userprofile%\lm_combined_certs.cer
@echo cafile=%userprofile%\lm_combined_certs.cer > %userprofile%\.npmrc
```

## bower

Create a file in your users home folder called **.bowerrc** with the following contents:

```
{
  "strict-ssl": true,
  "ca": "C:/Users/{user}/lm_combined_certs.cer"
}
```

Or use the script:

```
:: bower config

:: configure bower to use lm root CA
:: WARNING: this will replace your existing bower configuration
:: copy /Y .bowerrc %userprofile% > nul
@echo { > %userprofile%\.bowerrc
@echo   "strict-ssl": true, >> %userprofile%\.bowerrc
@echo   "ca": "%usershomeunix%/lm_combined_certs.cer" >> %userprofile%\.bowerrc
@echo } >> %userprofile%\.bowerrc
```

## pip

Create a folder in your app data folder (%appdata%) called "pip" with a file in it called "pip.ini" with the following contents:

```
[global] 
cert = C:\Users\{user}\lm_combined_certs.cer
```

Or use the script:

```
:: pip config

:: configure pip to use lm root CA
:: WARNING: this will replace your existing pip configuration
mkdir %appdata%\pip > nul
@echo [global] > %appdata%\pip\pip.ini
@echo cert = %userprofile%\lm_combined_certs.cer >> %appdata%\pip\pip.ini
```

## PyCharm

* Go to settings > HTTP Proxy.  
* Set to "Manual proxy configuration"
    * Host:  proxy-lmi.global.lmco.com  
    * Port 80
    * Make sure "proxy authentication" is unchecked
* Go to settings > Server Certificates
    * Remove any existing "accepted certificates" unless you know why they are there
    * Click the green "+" to add a certificate, and choose the LM Root Cert file.  If you run the config batch file it will be stored here: "C:/Users/{user}/lm_combined_certs.cer"
* Go back to the proxy settings and click "check connection" and test https://www.google.com, it should work

## Java / Maven

Import the keystore from the LM SharePoint site (assumes you have `lm_and_third_party.keystore` in your user folder (which it will be if you run init.bat), and also that `%JAVA_HOME%` is set and pointed to the java install of interest.
```
C:> keytool -importkeystore -srckeystore %USERPROFILE%\lm_and_third_party.keystore -destkeystore %JAVA_HOME%\lib\security\cacerts
Enter destination keystore password: changeit
Enter source keystore password: LMCO-PKI
```

NOTES:
 * the keystore passwords are literally 'changeit' and 'LMCO-PKI' so the above code block is correct verbatim (unless you changed the destination keystore password).  If you installed JRE with admin the file my be write-protected in which case you should uninstall Java with admin and then download the zipped version of JRE, exctract it somewhere, add it to your path and JAVA_HOME, then add the keystore with keytool as indicated above.
 * if you already have any of the same certificate/alias pairs in your Java keystore (cacerts), you will be prompted to overwrite the certificate or provide an alternate alias.
 * this will fail if you point it to an admin-installed version of Java in a write-protected folder

Then, copy `settings.xml` to the `C:\Users\{user}\.m2` folder (or run init.bat). Note that you may also want to set a custom folder for the Maven repository by adding the following line to your settings.xml file:
```
  <localRepository>D:\some\path\on\your\computer</localRepository>
```

## Requests

Set env var so that requests looks at uber cert:
```
:: requests config

:: set env var to tell the Requests python lib to use uber cert 
set REQUESTS_CA_BUNDLE=%userprofile%\lm_combined_certs.cer > nul
setx REQUESTS_CA_BUNDLE %userprofile%\lm_combined_certs.cer > nul
```
"# IS_550_Final_Project" 
"# IS_550_Final_Project" 
