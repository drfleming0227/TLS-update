# TLSv1.2 Requirement

The Payment Card Industry Security Standards Council (PCI SSC) [mandates](http://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls) that early versions of TLS be retired from service. **All credit card processors must make these changes** by the PCI deadline, so expect to see similar announcements from other payment providers that you might use. As part of this requirement, PayPal and Braintree are making this upgrade alongside the rest of the payments industry. 

PayPal and Braintree are updating its services to require TLS 1.2 for all HTTPS connections. PayPal and Braintree will also require HTTP/1.1 for all connections.

For more official, relevant information, see the [PayPal Technical Support website](https://www.paypal-techsupport.com):
* [TLS 1.2 and HTTP/1.1 Upgrade Microsite](https://www.paypal-knowledge.com/infocenter/index?page=content&id=FAQ1914&expand=true&locale=en_US)
* [2016-2017 Merchant Security Roadmap Microsite](https://www.paypal-knowledge.com/infocenter/index?page=content&id=FAQ1913&expand=true&locale=en_US)
* [SSL Certificate Upgrade Microsite](https://www.paypal-knowledge.com/infocenter/index?page=content&id=FAQ1766&expand=true&locale=en_US)

## What does this mean for PayPal and Braintree merchants?

Merchants must verify that their systems can use the TLSv1.2 protocol with a SHA-256 certificate. As a merchant, you must make sure that you are up-to-date with security updates including current versions of operating systems, encryption libraries, and runtime environments.

To get started, use the following notes to check the TLSv1.2 readiness for your environments:

* [Prerequisites](#prerequisites)
* [Java](#java)
* [.NET](#net)
* [PHP](#php)
* [Python](#python)
* [Ruby](#ruby)
* [Node](#node)
* [Native Mobile Apps](#native-mobile-apps)

### Prerequisites 

These checks assume that you have installed all the libraries required by the PayPal REST and Braintree SDKs. For these checks to be valid, run them on a production system or one that *exactly* matches the production configuration.

### Java

* [Java versions and TLS support](#java-versions-and-tls-support)
* [To check Java and TLS versions](#to-check-java-and-tls-versions)
* [Supported SDKs](#supported-sdks-java)

#### Java versions and TLS support

> **Note:** Java 8 is preferred because TLSv1.2 is the default in this Java version.

| Java&nbsp;version | TLSv1.2&nbsp;support | Requirements |
|:--------------|:-----------------|:--------------|
| 5&nbsp;and&nbsp;earlier | No support | <p>Upgrade to Java 6 or later.</p><blockquote><strong>Note:</strong> Java 8 is preferred because TLSv1.2 is the default in this Java version.</blockquote> |
| 6 | Available | <ul><li>You must explicitly enable TLSv1.2.</li><li>At least [Oracle Java version `6u115 b32`](http://www.oracle.com/technetwork/java/javase/documentation/overview-156328.html) or [IBM V6 service refresh 10](http://www-01.ibm.com/support/knowledgecenter/SSYKE2_6.0.0/com.ibm.java.security.component.60.doc/security-component/jsse2Docs/overrideSSLprotocol.html).</li><li>A [PayPal SDK update](PayPal/README.md#java) or code change might be required.</li></ul> |
| 7 | Available | <ul><li>You must explicitly enable TLSv1.2.</li><li>A [PayPal SDK update](PayPal/README.md#java) or code change might be required.</li><ul> |
| 8 | Default | <ul><li>No code change is required.</li><li>Make sure that you're using the latest [PayPal SDK](PayPal/README.md#java).</li></ul> |

#### To check your Java and TLS versions

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

    * On success:

        ```
        Successfully connected to TLS 1.2 endpoint.
        ```

    * On failure:

        ```
        Failed to connect to TLS 1.2 endpoint.
        ```

<a id="supported-sdks-java"></a>

#### Supported SDKs

* [PayPal](PayPal/README.md#java)
* [Braintree](Braintree/README.md#java)

### .NET

You must install the .NET 4.5 or later runtime for TLSv1.2 to be enabled.

* [To check your .NET and TLS versions](#to-check-your-net-and-tls-versions)
* [Supported SDKs](#supported-sdks-net)

#### To check your .NET and TLS versions

1. Set the TLS version through [`ServicePointManager.SecurityProtocol`](https://msdn.microsoft.com/en-us/library/system.net.securityprotocoltype(v=vs.110).aspx).

1. To verify that you have .NET framework 4.5 or later, run [NetFrameworkVersions](net/NetFrameworkVersions) on the console of your production system. If you do not have .NET 4.5 or later, upgrade it.

1. In a shell on your **production system**, run [TlsCheck](net/TlsCheck):

    ```
    > TlsCheck.exe
    ```

    * On success:

        ```
        PayPal_Connection_OK
        ```

<a id="supported-sdks-net"></a>

#### Supported SDKs

* [PayPal](PayPal/README.md#net)
* [Braintree](Braintree/README.md#net)

### PHP

PHP uses the system-supplied cURL library, which requires OpenSSL 1.0.1c or later. You might also need to [update your SSL/TLS libraries](http://curl.haxx.se/docs/ssl-compared.html).

* [Guidelines](#guidelines)
* [To check your PHP and TLS versions](#to-check-your-php-and-tls-versions)

#### Guidelines

Find OpenSSL in these locations:

1. OpenSSL installed in your operating system's `openssl version`.

1. OpenSSL extension installed in your PHP. Find this in your `php.ini`.

<a id="option-3"></a> 1. OpenSSL used by PHP_CURL.`curl_version()`.

These OpenSSL extensions can be different, and you update each one separately.

The one that PayPal or any other PHP SDK uses to make HTTP connections is the one used by [PHP_CURL](#option-3). The PHP_CURL OpenSSL must support TLSv1.2.

The `php_curl` library uses its own version of the OpenSSL library, which is not the same version that PHP uses, which is the `openssl.so` file in `php.ini`.

#### To check your PHP and TLS versions

1. To find the `openssl_version` information for cURL, run:

    ```
    php -r 'echo json_encode(curl_version(), JSON_PRETTY_PRINT);'
    ```

    The `php_curl` version shown here might be different from the `openssl version`, because they are different components.

1. When you update your OpenSSL libraries, you must update the `php_curl` OpenSSL version, and not the OS OpenSSL version.

1. In a shell on your **production system**:

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

These actions help you determine your openssl version.

> **Note:** Make sure that your command line test uses the same versions of PHP and SSL/TLS libraries as your web server

> **Note:** If you are use MAMP or XAMPP as your development set up, the PHP that is packaged with them uses an earlier version of OpenSSL, which you cannot easily update. For more information about this issue and a temporary workaround , see [Unknown SSL protocol error](https://github.com/paypal/PayPal-PHP-SDK/issues/484#issuecomment-176240130).

### Python

Python uses the system-supplied OpenSSL. TLSv1.2 requires OpenSSL 1.0.1c or later.

* [To check your Python and TLS versions](#to-check-your-python-and-tls-versions)

#### To check your Python and TLS versions

1. In a shell on your **production system**, run: 

    For Python 2.x:

    ```
    $ python -c "import urllib2; print(urllib2.urlopen('https://tlstest.paypal.com/').read())"
    ```

    For Python 3.x:

    ```
    $ python -c "import urllib.request; print(urllib.request.urlopen('https://tlstest.paypal.com/').read())"
    ```

    * On success:
        ```
        PayPal_Connection_OK
        ```
    * On failure, an `URLError` is raised:
        ```
        urllib2.URLError: <urlopen error EOF occurred in violation of protocol (_ssl.c:590)>
        ```
        ```
        urllib2.URLError: <urlopen error [Errno 54] Connection reset by peer>
        ```

### Ruby

Ruby 2.0.0 or above is required to use TLSv1.2 from the system supplied OpenSSL. TLSv1.2 requires OpenSSL 1.0.1c or later. Therefore, both `Ruby > 2.0.0` and `OpenSSL > 1.0.1c` are required. You may also need to run `bundle update` to update your dependencies.

*For the PayPal legacy Ruby SDK (packaged as `PP_Ruby_NVP_SDK.zip`), download [this update](https://github.com/paypal/TLS-update/blob/master/ruby/PP_Ruby_NVP_SDK.zip).*

* [To check your Ruby and TLS versions](#to-check-your-ruby-and-tls-versions)

#### To check Ruby and TLS versions

1. In a shell on your **production system**, run:

    ```
    $ ruby -r'net/http' -e 'puts Net::HTTP.get(URI("https://tlstest.paypal.com/"))'
    ```

        * On success:
    
            ```
            PayPal_Connection_OK
            ```

        * On failure, a `OpenSSL::SSL::SSLError` or `EOFError` is thrown.

### Node

Node.js uses the system supplied OpenSSL. TLSv1.2 requires OpenSSL 1.0.1c or later.

* [To check your Node and TLS versions](#to-check-your-node-and-tls-versions)

#### To check Node and TLS versions

1. In a shell on your **production system**, run:

    ```
    $ node -e "var https = require('https'); https.get('https://tlstest.paypal.com/', function(res){ console.log(res.statusCode) });"
    ```

    * On success:
        
        ```
        200
        ```
    * On failure, a network error occurs.

## Native Mobile Apps

* [Android](#android)
* [iOS](#ios)
* [Windows](#windows)

### Android

TLSv1.2 was made default for client connections in API 20 (Android 4.4W "KitKat - wearable extensions").

All Android app developers will want to make sure their code and PayPal or Braintree SDKs provide explicit support for TLSv1.2. Apps should be tested on Android 4.1-4.4 (API 16-19) devices to verify correct implementation.

After the TLSv1.2 upgrade, native app support for user devices older than API 16 (Android 4.1 "Jelly Bean") will not be available. Fortunately, as of December 5, 2016, [Google reports 2.5% of devices accessing the Play store are API 15 or earlier](http://developer.android.com/about/dashboards/index.html#Platform).

Users of the PayPal or Braintree Android SDKs should simply update to the latest version. Outside of the SDK, we have provided [an example Android app](android/) to illustrate how to support TLSv1.2.

#### Supported SDKs

* [PayPal](PayPal/README.md#android)
* [Braintree](Braintree/README.md#android)

### iOS

TLSv1.2 support was introduced in iOS 5. The [PayPal iOS SDK](https://github.com/paypal/PayPal-iOS-SDK) and the [Braintree iOS SDK](https://github.com/braintree/braintree_ios) both require iOS 7 or later. Apps built since 2013 will likely not need any updates.

### Windows

Neither PayPal nor Braintree support any Windows SDKs. For a web browser integration, we recommend [Braintree v.zero for JavaScript](https://developers.braintreepayments.com/javascript+dotnet/guides/client-sdk).
