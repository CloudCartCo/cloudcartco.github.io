# Global Extension Field: `traceId`

In accordance with [RFC 9457 Section 3.2 (Extension Members)](https://www.rfc-editor.org/rfc/rfc9457.html#name-extension-members), `"traceId"` is a global extension field applicable to any Problem Type defined in this registry.

## Definition

| Field | Purpose | Value |
| :--- | :--- | :--- |
| **JSON Key** | **Identity** | `"traceId"` |
| **Data Type** | **Type** | `String` |
| **Requirement** | **Presence** | Optional (but **highly recommended**) |

## Purpose

The `"traceId"` is a statistically unique identifier generated on the server for each specific request. It allows API consumers to provide a unique reference ID when reporting, enabling the API provider to correlate a failed request back to the specific telemetry or internal logs generated at the time of the error.

## Implementation Note (.NET)

For .NET-based APIs, the following pattern ensures the `"traceId"` is always populated using the most helpful identifier available:

```csharp
builder.Services.AddProblemDetails(options =>
{
    options.CustomizeProblemDetails = context =>
    {
        var traceId = System.Diagnostics.Activity.Current?.TraceId.ToHexString() 
                      ?? context.HttpContext.TraceIdentifier;

        context.ProblemDetails.Extensions["traceId"] = traceId;
    };
});
```

**Understanding the logic:**

* **OpenTelemetry Trace Id (Preferred):** If an [OpenTelemetry pipeline](https://learn.microsoft.com/en-us/dotnet/core/diagnostics/observability-with-otel) has been initialized, the `traceId` will be the TraceId of the active trace. This makes it really easy to correlate a Problem Detail response returned from the API to the trace that was active at the time. 
* **TraceIdentifier (Fallback):** If the OpenTelemetry pipeline was not initialized (e.g., you are not using OpenTelemetry in your API project), then this would be the `HttpContext.TraceIdentifier`. This value can be correlated back to a logged request if you have [HTTP Logging](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/http-logging/) or [W3C Logging](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/w3c-logger/) enabled in your application.
