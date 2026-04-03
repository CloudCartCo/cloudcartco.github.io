# Problem Type: Validation Error

This page defines the standard problem type used by CloudCartCo APIs when a request fails semantic validation (e.g., business rule violations or invalid field values).

## Definition

| Field | Purpose | Value |
| :--- | :--- | :--- |
| `"type"` | **Identity** | `https://cloudcartco/problemregistry/problems/validation-error` |
| `"title"` | **Summary** | One or more validation errors occurred. |
| `"status"` | **Status Code** | 422 |
| `"errors"` | **Extension Field** | An extension field to describe the all the validation errors that are to be communicated to the API caller. |

## Description

This problem type is used when a request is syntactically correct (e.g., valid JSON) but the values provided do not meet the application's requirements. While many frameworks default `status` in a Problem Details response for validation errors to a `400 Bad Request`, this problem type uses [**422 Unprocessable Entity**](https://datatracker.ietf.org/doc/html/rfc4918#section-11.2) HTTP status code to distinguish between malformed requests and valid requests that contain invalid data.

## Extension Members

Extension fields specific to this problem type are documented above (e.g. `"errors"`). These follow the extension mechanism defined in [RFC 9457 Section 3.2](https://www.rfc-editor.org/rfc/rfc9457.html#name-extension-members).

In addition, the Problem Details response may contain global extension fields; these are applicable to responses associated with any Problem Type in this registry and can be found at [https://cloudcartco.github.io/problemregistry/extensions](https://cloudcartco.github.io/problemregistry/extensions).

## Example Response

The following is a sample payload for this problem type, including the required `errors` object and the [`traceId`](https://cloudcartco.github.io/problemregistry/extensions/traceId) global extension field used for log correlation.

```json
{
  "type": "https://cloudcartco/problemregistry/problems/validation-error",
  "title": "One or more validation errors occurred.",
  "status": 422,
  "detail": "The request contained invalid data in the payload.",
  "instance": "/orders/checkout",
  "errors": {
    "Quantity": [
      "The Quantity field must be at least 1."
    ],
    "CouponCode": [
      "The coupon code 'DISCOUNT10' is no longer valid."
    ]
  },
  "traceId": "00-84e1b3638787d5830761e86532f8623a-592f75043640b79b-00"
}
