# AWS WAF Metrics<a name="waf-metrics"></a>

The `WAF` namespace includes the following metrics\.


| Metric | Description | 
| --- | --- | 
| `AllowedRequests` |  The number of allowed web requests\. Reporting criteria: There is a nonzero value Units: Count Dimensions: `Rule`, `WebACL` Valid statistics: Sum  | 
| `BlockedRequests` |  The number of blocked web requests\. Reporting criteria: There is a nonzero value Units: Count Dimensions: `Rule`, `WebACL` Valid statistics: Sum  | 
| `CountedRequests` |  The number of counted web requests\. Reporting criteria: There is a nonzero value A counted web request is one that matches all of the conditions in a particular rule\. Counted web requests are typically used for testing\. Units: Count Dimensions: `Rule`, `WebACL` Valid statistics: Sum  | 