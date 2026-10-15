---
description: >-
  Use the Visitor-User-Agent header to pass a visitor's User-Agent when a
  server-side frontend calls the Umbraco Engage API or the Content Delivery API.
---

# Visitor-User-Agent header

When a server-side frontend calls Umbraco on behalf of a visitor, the request comes from that server, not from the visitor's browser. The request's `User-Agent` header describes the server's HTTP client, such as `axios/1.7.2` or `node`.

Umbraco Engage reads the `User-Agent` to determine the visitor's device, browser, and operating system. Segment rules such as **Device type** and **Browser** depend on these values. Evaluated against a server's `User-Agent`, a rule like **Device type is Mobile** never matches.

Send the visitor's own `User-Agent` in the `Visitor-User-Agent` header. Umbraco Engage then uses that value instead of the request's `User-Agent`.

## When to use it

Send the header on every request a server-side frontend, such as a `Next.js` or `Nuxt` server, makes on a visitor's behalf:

* Content Delivery API v2 requests that return personalized or A/B tested content.
* Requests to the Umbraco Engage API segmentation endpoints, such as `activesegments` and `assets`.

You do not need the header when the visitor's browser calls Umbraco directly. Those requests already carry the visitor's `User-Agent`.

## Supported endpoints

The `Visitor-User-Agent` header is honored on the following routes:

* **Umbraco Content Delivery API v2**, for any request starting with `/umbraco/delivery/api/v2/content`. This covers:
  * `/umbraco/delivery/api/v2/content/item/{id}`
  * `/umbraco/delivery/api/v2/content/item/{path}`
  * `/umbraco/delivery/api/v2/content/items`
  * `/umbraco/delivery/api/v2/content`
* **Umbraco Engage API v1**, for any request starting with:
  * `/umbraco/engage/api/v1/analytics/`
  * `/umbraco/engage/api/v1/segmentation/`

{% hint style="warning" %}
The header is ignored on the Content Delivery API **v1** (`/umbraco/delivery/api/v1/...`). Target the v2 endpoints from a server-side frontend.
{% endhint %}

## Example request

The following request fetches a page from the Content Delivery API v2 for a visitor browsing on an iPhone:

```http
GET /umbraco/delivery/api/v2/content/item/ca4249ed-2b23-4337-b522-63cabe5587d1 HTTP/1.1
Host: your-site.com
Accept: application/json
External-Visitor-Id: 3fa85f64-5717-4562-b3fc-2c963f66afa6
Visitor-User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 18_5 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.5 Mobile/15E148 Safari/604.1
```

In a server-side frontend, copy the `User-Agent` header of the visitor's request to your server into the `Visitor-User-Agent` header. The following function shows the pattern:

{% code title="engage-content.ts" %}
```typescript
// Fetches a content item from the Content Delivery API on behalf of a visitor.
// visitorUserAgent is the User-Agent header of the visitor's request to your frontend server.
export async function getContentForVisitor(
  umbracoUrl: string,
  contentId: string,
  externalVisitorId: string,
  visitorUserAgent: string,
): Promise<unknown> {
  const response = await fetch(`${umbracoUrl}/umbraco/delivery/api/v2/content/item/${contentId}`, {
    headers: {
      'External-Visitor-Id': externalVisitorId,
      'Visitor-User-Agent': visitorUserAgent,
    },
  });

  return response.json();
}
```
{% endcode %}

## Tracking pageviews from the server

The `trackpageview/server` endpoint accepts the visitor's `User-Agent` in the `browserUserAgent` property of the request body. You can use either the property or the header on that endpoint. If a request sends both, the `Visitor-User-Agent` header takes precedence.

## Requests made before the pageview is processed

Umbraco Engage remembers the result of some segment rules for each visitor, including the **Device type** and **Browser** rules. In a headless setup, these results are remembered when Umbraco Engage processes the visitor's tracked pageviews. Requests to the Content Delivery API and the segmentation endpoints use the remembered results but never store their own.

Tracked pageviews are processed in batches. A batch runs every `FlushIntervalInSeconds` (30 seconds by default), or sooner once `FlushRateInRecords` pageviews are waiting. For more information, see [Configuration](../settings/configuration.md).

A request made before the visitor's first pageview is processed has no remembered result to use. Umbraco Engage evaluates the rules against that request's own `User-Agent`. Without the `Visitor-User-Agent` header, the request can return content for the wrong segment. Later requests use the result from the tracked pageview.

{% hint style="info" %}
Send the `Visitor-User-Agent` header on every request to get the correct segments from the first request onwards.
{% endhint %}

## Limitations

Keep the following in mind when using `Visitor-User-Agent`:

* **Only active when Engage is enabled.** If the Engage app state is disabled, the header is ignored.
* **Empty values are ignored.** Umbraco Engage then falls back to the request's `User-Agent` header.
* **Not validated.** Any value is accepted. A value Umbraco Engage cannot recognize is treated like any other unrecognized `User-Agent`, with an unknown device type.
* **The request itself is unchanged.** Only Umbraco Engage reads the header. Other parts of Umbraco and your hosting environment still see the request's `User-Agent`.
* **Scoped to Content Delivery API v2 and Engage API v1.** The header has no effect on other routes or on the Content Delivery API v1.

## Related reading

* [Using the Engage API](using-the-marketing-api.md) — the standard visitor-driven flow.
* [Forced-Segment header](forced-segment.md) — request a specific variant directly.
* [Configuration](../settings/configuration.md) — including the pageview flush settings.
