# Errors

The Codex API can return the following error codes:

Error Code | Error Description
---------- | -----------------
401        | Unauthorized Error - Your access token is invalid (e.g. it has expired and you need to request a new one.)
402        | Payment Required Error - You do not have enough [CODX](#codx-tokens-amp-fees) to perform the requested action. On testnets, you may [request more CODX from the faucet](#get-codx-from-faucet). On Mainnet, you must [purchase additional CODX](#purchase-codx).
403        | Forbidden Error - Your access token is valid, but you do not have permission to perform the request action (e.g. "admin only" actions.)
404        | Not Found Error - The requested resource (or route) does not exist.
409        | Missing Parameter Error - A required parameter was not specified in the request data. See error message for details.
409        | Invalid Argument Error - An invalid parameter was specified in the request data (e.g. an invalid email address), or the requested action can not be performed (e.g. cancelling a transfer that hasn't been initiated yet.) See error message for details.
409        | Conflict Error - The requested resource could not be created because a record with a matching unique identifier already exists. See error message for details.
500        | Internal Server Error - There was an unknown problem processing the request. Please try again later.
503        | Service Unavailable - We're temporarily offline for maintenance. Please try again later.
