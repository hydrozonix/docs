---
description: How to update user metadata properties.
crews: crew-2
topics:
  - metadata
  - lock
contentType: how-to
useCase: manage-users
v2: true
---

# Update Metadata Properties

You can update a user's metadata by making a `PATCH` call to the [Update User endpoint of the Management API](/api/management/v2#!/Users/patch_users_by_id).

Assuming you created the user as shown above with the following metadata values:

```json
{
    "email": "jane.doe@example.com",
    "user_metadata": {
        "hobby": "surfing"
    },
    "app_metadata": {
        "plan": "full"
    }
}
```

To update `user_metadata` and add the user's home address as a second-level property:

```json
{
    "addresses": {
        "home": "123 Main Street, Anytown, ST 12345"
    }
}
```

You would make the following `PATCH` call:

```har
{
  "method": "PATCH",
  "url": "https://${account.namespace}/api/v2/users/user_id",
  "httpVersion": "HTTP/1.1",
  "cookies": [],
  "headers": [{
    "name": "Authorization",
    "value": "Bearer ABCD"
  }, {
    "name": "Content-Type",
    "value": "application/json"
  }],
  "queryString": [],
  "postData": {
    "mimeType": "application/json",
    "text": "{\"user_metadata\": {\"addresses\": {\"home\": \"123 Main Street, Anytown, ST 12345\"}}}"
  },
  "headersSize": -1,
  "bodySize": -1,
  "comment": ""
}
```

The user's profile will now appear as follows:

```json
{
    "email": "jane.doe@example.com",
    "user_metadata": {
        "hobby": "surfing",
        "addresses": {
            "home": "123 Main Street, Anytown, ST 12345"
        }
    },
    "app_metadata": {
        "plan": "full"
    }
}
```

::: warning
When you send a `PATCH` call in which you have set a property's value to `null` (for example, `{user_metadata: {color: null}}`), Auth0 **deletes** the property/value from the database. Also, patching the metadata itself with an empty object removes the metadata completely (see [Deleting](#deleting)).
:::

## Merging properties

Only properties at the root level are merged into the object. All lower-level properties will be replaced.

For example, to add a user's work address as an additional inner property, you would have to include the complete contents of the `addresses` property. Since the `addresses` object is a root-level property, it will be merged into the final JSON object representing the user, but its sub-properties will not.

```json
{
  "user_metadata": {
    "addresses": {
      "home": "123 Main Street, Anytown, ST 12345",
      "work": "100 Industrial Way, Anytown, ST 12345"
    }
  }
}
```

Therefore, the corresponding `PATCH` call to the API would be:

```har
{
  "method": "PATCH",
  "url": "https://YOURACCOUNT.auth0.com/api/v2/users/user_id",
  "httpVersion": "HTTP/1.1",
  "cookies": [],
  "headers": [{
    "name": "Authorization",
    "value": "Bearer ABCD"
  }, {
    "name": "Content-Type",
    "value": "application/json"
  }],
  "queryString": [],
  "postData": {
    "mimeType": "application/json",
    "text": "{\"user_metadata\": {\"addresses\": {\"home\": \"123 Main Street, Anytown, ST 12345\", \"work\": \"100 Industrial Way, Anytown, ST 12345\"}}}"
  },
  "headersSize": -1,
  "bodySize": -1,
  "comment": ""
}
```

## Deleting properties

Patching the metadata with an empty object removes the metadata completely. For example, sending this body removes everything in `app_metadata`:

```json
{
  "app_metadata": {}
}
```

Similarly, this clears out `user_metadata`:

```json
{
  "user_metadata": {}
}
```
## Sample request

Here is a sample request that adds the user's home address as a second-level property:

```har
{
  "method": "PATCH",
  "url": "https://YOURACCOUNT.auth0.com/api/v2/users/user_id",
  "httpVersion": "HTTP/1.1",
  "cookies": [],
  "headers": [{
    "name": "Authorization",
    "value": "Bearer YOUR_TOKEN"
  }, {
    "name": "Content-Type",
    "value": "application/json"
  }],
  "queryString": [],
  "postData": {
    "mimeType": "application/json",
    "text": "{\"user_metadata\": {\"addresses\": {\"home\": \"123 Main Street, Anytown, ST 12345\"}}}"
  },
  "headersSize": -1,
  "bodySize": -1,
  "comment": ""
}
```

::: note
The Auth0 Management APIv2 token is required to call the Auth0 Management API. For more information see [Access Tokens for the Management API](/api/management/v2/concepts/tokens)
:::