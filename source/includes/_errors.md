# Errors
Hako uses standard `HTTP` response codes for success and failure notifications. In general, **200** HTTP codes correspond to success, **40x** codes are for developer- or user-related failures, and **50x** codes are for Hako-related issues.

All errors returned by the API include an **error message key/value pair** under the key `error`. Its value will be a string description of the error, which is subject to change and is not safe for programmatic use.

We are working to include a safe key/value pair for more accurate error handling.

The Hako API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request
401 | Unauthorized request -- your API key is missing or wrong.
404 | Not Found
429 | Too Many Requests
500 | Internal Server Error -- We had a problem with our server. Try again later.
