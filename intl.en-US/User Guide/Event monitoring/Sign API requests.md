# Sign API requests {#concept_gyv_r3b_wdb .concept}

This topic discusses authenticating monitoring events by generating API request signatures. Currently, event reporting only supports the signature algorithm HMAC-SHA1, which is also the default algorithm.

1.  Prepare the effective Alibaba Cloud access key.

    To generate a signature for an API request, a pair of access key \(namely AccessKeyId and AccessKeySecret\) is required. You can use an existing pair or create a new pair for this step. However, the pair you use must be enabled.

2.  Generate the API request signature string.

    The API signature string is generated by the method, header, and body elements in the HTTP request.

    ```
    SignString = VERB + "\n"
                 + CONTENT-MD5 + "\n"
                 + CONTENT-TYPE + "\n"
                 + DATE + "\n"
                 + CanonicalizedHeaders + "\n"
                 + CanonicalizedResource
    ```

    In the preceding formula, `\n` is used to start a new line, and the plus sign \(`+`\) is used to concatenate strings. The elements in the formula are defined as follows.

    |Element|Description|Example|
    |:------|:----------|:------|
    |VERB|The method name for an HTTP request|PUT, GET, or POST|
    |CONTENT-MD5|The MD5 value of the body of an HTTP request \(which must be an uppercase string\)|875264590688CA6171F6228AF5BBB3D2|
    |CONTENT-TYPE|The type of the body of a request|application/json|
    |DATE|The standard timestamp header in an HTTP request \(following the RFC 1123 time format and using GMT Standard Time\)|Mon, 3 Jan 2010 08:33:47 GMT|
    |CanonicalizedHeaders|A string constructed by a custom header prefixed by X-CMS and X-ACS in an HTTP request|x-cms-api-version:0.1.0\\nx-cms-signature|
    |CanonicalizedResource|A string constructed from an HTTP request resource|/event/custom/upload|

    **Note:** The construction methods for the CanonicalizedHeaders and CanonicalizedResource elements are discussed in more detail in the description that follows.

    Construct the CanonicalizedHeaders element

    1.  Convert each HTTP request header prefixed with `x-cms` and `x-acs` to lowercase.
    2.  Sort the CMS custom request headers obtained in the preceding step lexicographically by header.
    3.  Remove any spaces at either end of `\n` between the request headers and content.
    4.  Separate all the headers and content by using `\n` to construct the final CanonicalizedHeader.
    Construct the CanonicalizedResource element

    1.  Set the CanonicalizedResource element to an empty string \(""\).
    2.  Enter the URI you want to access, such as `/event/custom/upload`.
    3.  If the request contains a query string \(`QUERY_STRING`\), then add a question mark \(`？`\) and the query string at the end of the CanonicalizedResource string.

        `QUERY_STRING` is a string in a URL where the request parameters are sorted lexicographically. In the query string, the parameter names and values are connected by equal signs \(`=`\) to form strings, and the parameter name-value pairs are sorted lexicographically and connected by ampersands \(`&`\). The formula is as follows.

        ```
        QUERY_STRING = "KEY1=VALUE1" + "&" + "KEY2=VALUE2"
        ```

3.  Generate the API request signature.

    The default signature algorithm HMAC-SHA1 is used. The entire signature formula is as follows.

    ```
    Signature = base16(hmac-sha1(UTF8-Encoding-Of(SignString)，AccessKeySecret))
    ```

