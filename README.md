# TLSv1.2 Requirement

The Payment Card Industry Security Standards Council (PCI SSC) [mandates](http://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls) that early versions of TLS be retired from service. **All credit card processors must make these changes** by the PCI deadline, so expect to see similar announcements from other payment providers that you might use. As part of this requirement, PayPal and Braintree are making this upgrade alongside the rest of the payments industry. 

PayPal and Braintree are updating its services to require TLS 1.2 for all HTTPS connections. PayPal and Braintree will also require HTTP/1.1 for all connections.

For more official, relevant information, see the [PayPal Technical Support website](https://www.paypal-techsupport.com):
* [TLS 1.2 and HTTP/1.1 Upgrade Microsite](https://www.paypal-knowledge.com/infocenter/index?page=content&id=FAQ1914&expand=true&locale=en_US)
* [2016-2017 Merchant Security Roadmap Microsite](https://www.paypal-knowledge.com/infocenter/index?page=content&id=FAQ1913&expand=true&locale=en_US)
* [SSL Certificate Upgrade Microsite](https://www.paypal-knowledge.com/infocenter/index?page=content&id=FAQ1766&expand=true&locale=en_US)

## What does this mean for PayPal and Braintree merchants?

Merchants must verify that all their systems can use the TLSv1.2 protocol with a SHA-256 certificate. Make sure that you are up-to-date with security updates including current versions of operating systems, encryption libraries, and runtime environments.

To help you get started, use the following notes to check the TLSv1.2 readiness for your environments:

* [Prerequisites](#prerequisites)
* [Java](#java)
* [.NET](#net)
* [PHP](#php)
* [Python](#python)
* [Ruby](#ruby)
* [Node](#node)
* 

### Prerequisites 

These checks assume that you have installed all the libraries required by the PayPal REST and Braintree SDKs. For these checks to be valid, run them on a production system or one that *exactly* matches the production configuration.

### Java

#### Java versions and TLS support

**The latest Java (currently 8) is preferred.** In Java 8, TLSv1.2 is the default.

| Java&nbsp;version | TLSv1.2&nbsp;support | Requirements |
|:--------------|:-----------------|:--------------|
| 5&nbsp;and&nbsp;earlier | No support | |
| 6 | Available | <ul><li>You must explicitly enable TLSv1.2.</li><li>At least [Oracle Java version `6u115 b32`](http://www.oracle.com/technetwork/java/javase/documentation/overview-156328.html) or [IBM V6 service refresh 10](http://www-01.ibm.com/support/knowledgecenter/SSYKE2_6.0.0/com.ibm.java.security.component.60.doc/security-component/jsse2Docs/overrideSSLprotocol.html).</li><li>A [PayPal SDK update](PayPal/README.md#java) or code change might be required.</li></ul> |
| 7 | Available | <ul><li>You must explicitly enable TLSv1.2.</li><li>A [PayPal SDK update](PayPal/README.md#java) or code change might be required.</li><ul> |
| 8 | Default | <ul><li>No code change is required.</li><li>Make sure that you're using the latest [PayPal SDK](PayPal/README.md#java).</li></ul> |

#### To check Java:

1. Set the TLS version through [`SSLContext`](http://docs.oracle.com/javase/7/docs/api/javax/net/ssl/SSLContext.html).
1. Verify that Java runtime 6 or later is installed:

    ```
    java -version
    ```

    If you have Java 5 or earlier, upgrade it. 

1. Download [the provided test application](java).

1. In a shell on your **production system**, run:

    ```
    > java -jar TlsCheck.jar
    ```

    * On success, this message appears:

        ```
        Successfully connected to TLS 1.2 endpoint.
        ```

    * On failure, this message appears:

        ```
        Failed to connect to TLS 1.2 endpoint.
        ```

#### Supported SDKs

- [PayPal](PayPal/README.md#java)
- [Braintree](Braintree/README.md#java)

### .NET

The .NET 4.5 (or greater) runtime must be installed for TLSv1.2 to be enabled.
  
1. Set the TLS version through [`ServicePointManager.SecurityProtocol`](https://msdn.microsoft.com/en-us/library/system.net.securityprotocoltype(v=vs.110).aspx).

1. To verify that you have .NET framework 4.5 or later, run [NetFrameworkVersions](net/NetFrameworkVersions) on the console of your production system. If you do not have .NET 4.5 or above, upgrade it.

1. In a shell on your **production system**, run [TlsCheck](net/TlsCheck):

    ```
    > TlsCheck.exe
    ```

    * On success, this message appears:

    ```
    PayPal_Connection_OK
    ```

#### Supported SDKs

- [PayPal](PayPal/README.md#net)
- [Braintree](Braintree/README.md#net)

### PHP

PHP uses the system supplied cURL library, which requires OpenSSL `1.0.1c` or later. You might also need to [update your SSL/TLS libraries](http://curl.haxx.se/docs/ssl-compared.html).

> NOTE:
* Find OpenSSL in these places:
    * OpenSSL installed in your Operating System `openssl version`.
    * OpenSSL extension installed in your PHP.  This can be found in your `php.ini`.
    * OpenSSL used by PHP_CURL. `curl_version()`.
* These OpenSSL extensions can be different, and you can update each separately.
* The one PayPal or any other PHP SDK uses to make HTTP connections is the PHP_CURL (option 3). The PHP_CURL OpenSSL must support TLSv1.2.
* `php_curl` library uses its own version of the OpenSSL library, which is not the same version as the one that PHP uses (`openssl.so` file that is  in `php.ini`).
* To find the `openssl_version` information for cURL, run:
    ```
    php -r 'echo json_encode(curl_version(), JSON_PRETTY_PRINT);'
    ```
* The `php_curl` version shown here could be different from the `openssl version`, as they are two different components.
* When you update your OpenSSL libraries, you must update `php_curl` OpenSSL version and not the OS OpenSSL version.

To check PHP:

In a shell on your **production system**:

1. Download [cacert.pem](php/cacert.pem).
1. Download [TlsCheck.php](php/TlsCheck.php).
1. Run `php -f TlsCheck.php`.

    * On success:
        ```
        PayPal_Connection_OK
        ```
    
    * On failure:
        ```
        curl_error information
        ```

This will help you determine the openssl version used.

> **Note:** Make sure that your command line test uses the same versions of PHP and SSL/TLS libraries as your web server
> **Note:** If you are using MAMP or XAMPP as your development setup, currently the PHP packaged with them comes with a lower version of OpenSSL, which currently cannot be updated easily. You can find more information on this issue and find a temporary workaround [here](https://github.com/paypal/PayPal-PHP-SDK/issues/484#issuecomment-176240130)

### Python

Python uses the system-supplied OpenSSL. TLSv1.2 requires OpenSSL 1.0.1c or later.

To check Python, in a shell on your **production system**, run: 

For Python 2.x:

`$ python -c "import urllib2; print(urllib2.urlopen('https://tlstest.paypal.com/').read())"`

For Python 3.x:

`$ python -c "import urllib.request; print(urllib.request.urlopen('https://tlstest.paypal.com/').read())"`

- On success, `PayPal_Connection_OK` is printed.
- On failure, an `URLError` will be raised: <br/>
`urllib2.URLError: <urlopen error EOF occurred in violation of protocol (_ssl.c:590)>`
`urllib2.URLError: <urlopen error [Errno 54] Connection reset by peer>`

### Ruby

Ruby 2.0.0 or above is required to use TLSv1.2 from the system supplied OpenSSL. TLSv1.2 requires OpenSSL 1.0.1c or later. Therefore, both `Ruby > 2.0.0` and `OpenSSL > 1.0.1c` are required. You may also need to run `bundle update` to update your dependencies.

*For the PayPal legacy Ruby SDK (packaged as `PP_Ruby_NVP_SDK.zip`), please download [this update](https://github.com/paypal/TLS-update/blob/master/ruby/PP_Ruby_NVP_SDK.zip).*

To check Ruby:

1. In a shell on your **production system**, run:

    ```
    $ ruby -r'net/http' -e 'puts Net::HTTP.get(URI("https://tlstest.paypal.com/"))'
    ```

        * On success:
    
            ```
            PayPal_Connection_OK
            ```

        * On failure, a `OpenSSL::SSL::SSLError` or `EOFError` is thrown.

### Node.js

Node.js uses the system supplied OpenSSL. TLSv1.2 requires OpenSSL 1.0.1c or later.

To check Node, in a shell on your **production system**, run:

`$ node -e "var https = require('https'); https.get('https://tlstest.paypal.com/', function(res){ console.log(res.statusCode) });"`

* On success:
    ```
    200
    ```
* On failure, a network error occurs.

## Native Mobile Apps

### Android

TLSv1.2 was made default for client connections in API 20 (Android 4.4W "KitKat - wearable extensions").

All Android app developers will want to make sure their code and PayPal or Braintree SDKs provide explicit support for TLSv1.2. Apps should be tested on Android 4.1-4.4 (API 16-19) devices to verify correct implementation.

After the TLSv1.2 upgrade, native app support for user devices older than API 16 (Android 4.1 "Jelly Bean") will not be available. Fortunately, as of December 5, 2016, [Google reports 2.5% of devices accessing the Play store are API 15 or earlier](http://developer.android.com/about/dashboards/index.html#Platform).

Users of the PayPal or Braintree Android SDKs should simply update to the latest version. Outside of the SDK, we have provided [an example Android app](android/) to illustrate how to support TLSv1.2.

##### Supported SDKs
- [PayPal](PayPal/README.md#android)
- [Braintree](Braintree/README.md#android)

### iOS

TLSv1.2 support was introduced in iOS 5. The [PayPal iOS SDK](https://github.com/paypal/PayPal-iOS-SDK) and the [Braintree iOS SDK](https://github.com/braintree/braintree_ios) both require iOS 7 or later. Apps built since 2013 will likely not need any updates.

### Windows

Neither PayPal nor Braintree support any Windows SDKs. For a web browser integration, we recommend [Braintree v.zero for JavaScript](https://developers.braintreepayments.com/javascript+dotnet/guides/client-sdk).
