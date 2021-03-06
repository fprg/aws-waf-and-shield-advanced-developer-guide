# Working with Size Constraint Conditions<a name="web-acl-size-conditions"></a>

If you want to allow or block web requests based on the length of specified parts of requests, create one or more size constraint conditions\. A size constraint condition identifies the part of web requests that you want AWS WAF to look at, the number of bytes that you want AWS WAF to look for, and an operator, such as greater than \(>\) or less than \(<\)\. For example, you can use a size constraint condition to look for query strings that are longer than 100 bytes\. Later in the process, when you create a web ACL, you specify whether to allow or block requests based on those settings\.

Note that if you configure AWS WAF to inspect the request body, for example, by searching the body for a specified string, AWS WAF inspects only the first 8192 bytes \(8 KB\)\. If the request body for your web requests will never exceed 8192 bytes, you can create a size constraint condition and block requests that have a request body greater than 8192 bytes\.


+ [Creating Size Constraint Conditions](#web-acl-size-conditions-creating)
+ [Values That You Specify When You Create or Edit Size Constraint Conditions](#web-acl-size-conditions-values)
+ [Adding and Deleting Filters in a Size Constraint Condition](#web-acl-size-conditions-editing)
+ [Deleting Size Constraint Conditions](#web-acl-size-conditions-deleting)

## Creating Size Constraint Conditions<a name="web-acl-size-conditions-creating"></a>

When you create size constraint conditions, you specify filters that identify the part of web requests for which you want AWS WAF to evaluate the length\. You can add more than one filter to a size constraint condition, or you can create a separate condition for each filter\. Here's how each configuration affects AWS WAF behavior:

+ **One filter per size constraint condition** – When you add the separate size constraint conditions to a rule and add the rule to a web ACL, web requests must match all the conditions for AWS WAF to allow or block requests based on the conditions\. 

  For example, suppose you create two conditions\. One matches web requests for which query strings are greater than 100 bytes\. The other matches web requests for which the request body is greater than 1024 bytes\. When you add both conditions to the same rule and add the rule to a web ACL, AWS WAF allows or blocks requests only when both conditions are true\.

+ **More than one filter per size constraint condition** – When you add a size constraint condition that contains multiple filters to a rule and add the rule to a web ACL, a web request needs only to match one of the filters in the size constraint condition for AWS WAF to allow or block the request based on that condition\.

  Suppose you create one condition instead of two, and the one condition contains the same two filters as in the preceding example\. AWS WAF allows or blocks requests if either the query string is greater than 100 bytes or the request body is greater than 1024 bytes\.

**Note**  
When you add a size constraint condition to a rule, you also can configure AWS WAF to allow or block web requests that *do not* match the values in the condition\.

**To create a size constraint condition**

1. Sign in to the AWS Management Console and open the AWS WAF console at [https://console\.aws\.amazon\.com/waf/](https://console.aws.amazon.com/waf/)\. 

1. In the navigation pane, choose **Size constraints**\.

1. Choose **Create condition**\.

1. Specify the applicable filter settings\. For more information, see [Values That You Specify When You Create or Edit Size Constraint Conditions](#web-acl-size-conditions-values)\.

1. Choose **Add another filter**\.

1. If you want to add another filter, repeat steps 4 and 5\.

1. When you're finished adding filters, choose **Create size constraint condition**\.

## Values That You Specify When You Create or Edit Size Constraint Conditions<a name="web-acl-size-conditions-values"></a>

When you create or update a size constraint condition, you specify the following values: 

**Name**  
Type a name for the size constraint condition\.  
The name can contain only the characters A\-Z, a\-z, and 0\-9\. You can't change the name of a condition after you create it\.

**Part of the request to filter on**  
Choose the part of each web request for which you want AWS WAF to evaluate the length:    
**Header**  
A specified request header, for example, the `User-Agent` or `Referer` header\. If you choose **Header**, specify the name of the header in the **Header** field\.  
**HTTP method**  
The HTTP method, which indicates the type of operation that the request is asking the origin to perform\. CloudFront supports the following methods: `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, and `PUT`\.  
**Query string**  
The part of a URL that appears after a `?` character, if any\.  
**URI**  
The part of a URL that identifies a resource, for example, `/images/daily-ad.jpg`\.  
**Body**  
The part of a request that contains any additional data that you want to send to your web server as the HTTP request body, such as data from a form\.

**Header \(Only When "Part of the request to filter on" is "Header"\)**  
If you chose **Header** for **Part of the request to filter on**, choose a header from the list of common headers, or type the name of a header for which you want AWS WAF to evaluate the length\.

**Comparison operator**  
Choose how you want AWS WAF to evaluate the length of the query string in web requests with respect to the value that you specify for **Size**\.  
For example, if you choose **Is greater than** for **Comparison operator** and type **100** for **Size**, AWS WAF evaluates web requests for a query string that is longer than 100 bytes\.

**Size**  
Type the length, in bytes, that you want AWS WAF to watch for in query strings\.  
If you choose **URI** for the value of **Part of the request to filter on**, the **/** in the URI counts as one character\. For example, the URI `/logo.jpg` is nine characters long\.

**Transformation**  
A transformation reformats a web request before AWS WAF evaluates the length of the specified part of the request\. This eliminates some of the unusual formatting that attackers use in web requests in an effort to bypass AWS WAF\.   
If you choose **Body** for **Part of the request to filter on**, you can't configure AWS WAF to perform a transformation because only the first 8192 bytes are forwarded for inspection\. However, you can still filter your traffic based on the size of the HTTP request body and specify a transformation of **None**\. \(AWS WAF gets the length of the body from the request headers\.\)
Transformations can perform the following operations:    
**None**  
AWS WAF doesn't perform any text transformations on the web request before checking the length\.  
**Convert to lowercase**  
AWS WAF converts uppercase letters \(A\-Z\) to lowercase \(a\-z\)\.  
**HTML decode**  
AWS WAF replaces HTML\-encoded characters with unencoded characters:  

+ Replaces `&quot;` with `&`

+ Replaces `&nbsp;` with a non\-breaking space

+ Replaces `&lt;` with `<`

+ Replaces `&gt;` with `>`

+ Replaces characters that are represented in hexadecimal format, `&#xhhhh;`, with the corresponding characters

+ Replaces characters that are represented in decimal format, `&#nnnn;`, with the corresponding characters  
**Normalize whitespace**  
AWS WAF replaces the following characters with a space character \(decimal 32\):  

+ \\f, formfeed, decimal 12

+ \\t, tab, decimal 9

+ \\n, newline, decimal 10

+ \\r, carriage return, decimal 13

+ \\v, vertical tab, decimal 11

+ non\-breaking space, decimal 160
In addition, this option replaces multiple spaces with one space\.  
**Simplify command line**  
For requests that contain operating system command line commands, use this option to perform the following transformations:  

+ Delete the following characters: \\ " ' ^

+ Delete spaces before the following characters: / \(

+ Replace the following characters with a space: , ;

+ Replace multiple spaces with one space

+ Convert uppercase letters \(A\-Z\) to lowercase \(a\-z\)  
**URL decode**  
Decode a URL\-encoded request\.

## Adding and Deleting Filters in a Size Constraint Condition<a name="web-acl-size-conditions-editing"></a>

You can add or delete filters in a size constraint condition\. To change a filter, add a new one and delete the old one\.

**To add or delete filters in a size constraint condition**

1. Sign in to the AWS Management Console and open the AWS WAF console at [https://console\.aws\.amazon\.com/waf/](https://console.aws.amazon.com/waf/)\. 

1. In the navigation pane, choose **Size constraint**\.

1. Choose the condition that you want to add or delete filters in\.

1. To add filters, perform the following steps:

   1. Choose **Add filter**\.

   1. Specify the applicable filter settings\. For more information, see [Values That You Specify When You Create or Edit Size Constraint Conditions](#web-acl-size-conditions-values)\.

   1. Choose **Add**\.

1. To delete filters, perform the following steps:

   1. Select the filter that you want to delete\.

   1. Choose **Delete filter**\.

## Deleting Size Constraint Conditions<a name="web-acl-size-conditions-deleting"></a>

If you want to delete a size constraint condition, you need to first delete all filters in the condition and remove the condition from all the rules that are using it, as described in the following procedure\.

**To delete a size constraint condition**

1. Sign in to the AWS Management Console and open the AWS WAF console at [https://console\.aws\.amazon\.com/waf/](https://console.aws.amazon.com/waf/)\. 

1. In the navigation pane, choose **Size constraints**\.

1. In the **Size constraint conditions** pane, choose the size constraint condition that you want to delete\.

1. In the right pane, choose the **Associated rules** tab\.

   If the list of rules using this size constraint condition is empty, go to step 6\. If the list contains any rules, make note of the rules, and continue with step 5\.

1. To remove the size constraint condition from the rules that are using it, perform the following steps:

   1. In the navigation pane, choose **Rules**\.

   1. Choose the name of a rule that is using the size constraint condition that you want to delete\.

   1. In the right pane, select the size constraint condition that you want to remove from the rule, and then choose **Remove selected condition**\.

   1. Repeat steps b and c for all the remaining rules that are using the size constraint condition that you want to delete\.

   1. In the navigation pane, choose **Size constraint**\.

   1. In the **Size constraint conditions** pane, choose the size constraint condition that you want to delete\.

1. Choose **Delete** to delete the selected condition\.