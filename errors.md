# API Errors

Fuel Rats API errors follow the [JSONAPI error object format](https://jsonapi.org/format/#errors). 

Some important things to note about JSONAPI errors:

* JSONAPI errors have an "id" field with a unique identifier that can be provided to the API maintainer to cross reference your error with the server logs.
* the "code" field contains an identifier for the specific type of error that has occured, it is recommended to use this in the error handling rather than the HTTP status code, as there might be multiple types of errors using the same HTTP status code.
* the "source" object will contain specific information about the source of the problem, such as an invalid parameter or field value.


Here is a list of Fuel Rats API application error codes.

Error Code | HTTP Status | Description
---------|----------|---------
bad_request | 400 | The request could not be completed because the request body was malformed, invalid or in a different format than expected. |
unauthorized | 401 | The request could not be compeleted because authorization is required but was not provided, or invalid credentials were provided. |
forbidden | 403 | The request was forbidden, you do not have the required permissions to complete it. |
verification_required | 403 | The request could not be completed because it was made from an unverified session, the user needs to approve the session. |
reset_required | 403 | The request could not be completed because it was made from an account that needs to have its password reset before it can continue making requests. |
not_found | 404 | The request attempted to access a resource that does not exist. |
conflict | 409 | The request could not be completed because it conflicts with an existing resource, for example creating a user with an email that is in use. |
gone | 410 | This request cannot be completed because it attempted to use a resource that is no longer available. This error will occur if making any action while the user is suspended. |
payload_too_large | 413 | Request attempted to upload a payload that is too large for the server to accept. |
unsupported_media_type | 415 | Requested attempted to upload a file that is incompatible with the server. Error is given by the API when uploading an avatar in an unsupported image format. |
im_a_teapot | 418 | Request was made to a resource that is a teapot, why are you making requests to a teapot? |
unprocessable_entity | 422 | Request could not be completed because it contained parameters that cannot be accepted. A form field or query parameter might be invalid. |
too_many_requests | 429 | Request could not be completed because too many requests has been made from this client recently. You have probably exceeded the API's rate limit. |
unavailable_legal | 451 | Request could not be completed because it attempted to access a resource that is no longer available due to legal reasons, such as a DMCA takedown or request by law enforcement. |
internal_server | 500 | An internal error happened in the server while processing your request, this should not happen. |
not_implemented | 501 | You attempted to access an endpoint that is currently not implemented. |
service_unavailable | 503 | Request could not be completed because the service is temporarily unavailable. It might be under maintenance or heavy load. |