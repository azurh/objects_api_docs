# SPACES

## INTRODUCTION

In the Java SDK, and in terms of the Objects API, a **Space** is represented as an instance of `PNSpace`. A `PNSpace` is instantiated as follows:

```java
PNSpace pnSpace = new PNSpace("space_1", "foo");
```

The spaces's `id` and `name` are mandatory and as such need to be passed directly to the constructor.

Other properties (except the `id`) are settable via setters:

```java
pnSpace.setDescription("A lovely space.");

HashMap<String, Object> customSpaceMap = new HashMap<>();
customSpaceMap.put("color", "blue");
customSpaceMap.put("public", true);
pnSpace.setCustom(customSpaceMap);
```

The `PNSpace` class features other properties too, which are not settable directly. Instead, the server is responsible for them and assigns values in different scenarios (e.g. when getting a space from PubNub).

```java
pnSpace.getCreated(); // space creation date
pnSpace.getUpdated(); // space update date
pnSpace.getETag();    // etag
```

### Complete overview

#### Getters
| Method           |    Return type    | Description  |
| ---------------- | ---------- | ------------ |
| `getId()` |    `String`    | Returns the space's unique identifier.|
| `getName()` |    `String`    | Returns the space's name.|
| `getDescription()` |    `String`    | Returns the space's description.|
| `getCustom()` |    `Object`    | Returns the space's deserializable custom data.|
| `getCreated()` |    `String`    | Returns the date and time the object was created.|
| `getUpdated()` |    `String`    | Returns the date and time the object was updated.|
| `getETag()` |    `String`    | Returns the the object's content fingerprint used in conditional requests.|

#### Setters

| Method |    Parameter Type | Description |
| --------- | ------- |------------ |
| `PNSpace()` |`String, String`|Creates a new instance of `PNSpace` with `id` and `name` provided.|
| `setName()` |`String`|Sets the name of the space.|
| `setDescription()` |`String`|Sets the description of the space.|
| `setCustom()` |`Object`| Sets a space's custom data. Serialization is done automatically, but the end result must be a Json object with scalar values only.|

> **NOTE**: The setters are chainable and offer a fluent API as such.

## CREATE SPACE

### Description

Creates a space with the specified properties. Returns the created space object, optionally including the space's custom data object.

### Method(s)

To `Create a space` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.createSpace().space(pnSpace);
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `space` |    `PNSpace`  |   Yes    | The space you wish to create. |
| `includeFields` |   `PNSpaceFields[]`   |   No    | Array of additional/complex space attributes to include in response.|
| `sync` | `PNCreateSpaceResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNCreateSpaceResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Create a space

```java
PNSpace pnSpace = new PNSpace("space_1", "foo");

pubnub.createSpace()
.space(pnSpace)
.async(new PNCallback<PNCreateSpaceResult>() {
    @Override
    public void onResponse(PNCreateSpaceResult result, PNStatus status) {
        if (!status.isError()) {
            PNSpace space = result.getSpace(); // the created space object
            } else {
            status.getErrorData().getThrowable().printStackTrace();
        }
    }
});
```

#### Create a space and retreive its custom data

```java
PNSpace pnSpace = new PNSpace("space_1", "foo");

JsonObject customData = new JsonObject();
customData.addProperty("color", "red");
customData.addProperty("city", "SF");

pnSpace.setCustom(customData);

pubnub.createSpace()
    .space(pnSpace)
    .includeFields(PNSpaceFields.CUSTOM)
    .async(new PNCallback<PNCreateSpaceResult> () {
        @Override
        public void onResponse(PNCreateSpaceResult result, PNStatus status) {
            if (!status.isError()) {
                PNSpace space = result.getSpace(); // the created space object
                Object custom = space.getCustom(); // the requested custom payload (if any)
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

### Response

The `createSpace()` operation returns a `PNCreateSpaceResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `getSpace()` |    `PNSpace`    | Returns the created space.|

## GET SPACE

### Description

Returns the specified space object, optionally including the space's custom data object.

### Method(s)

To `Get a space` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.getSpace().spaceId("space_1");
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `spaceId` |    `String`  |   Yes    | The ID of the space to retrieve. |
| `includeFields` |   `PNGetSpaceResult`   |   No    | Additional JSON fields to be returned in the response. |
| `sync` | `PNGetSpaceResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNGetSpaceResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Get a space

```java
pubnub.getSpace()
    .spaceId("space_1")
    .async(new PNCallback<PNGetSpaceResult> () {
        @Override
        public void onResponse(PNGetSpaceResult result, PNStatus status) {
            if (!status.isError()) {
                PNSpace space = result.getSpace(); // the requested space object
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get a space with its custom data

```java
pubnub.getSpace()
    .spaceId("space_1")
    .includeFields(PNSpaceFields.CUSTOM)
    .async(new PNCallback<PNGetSpaceResult> () {
        @Override
        public void onResponse(PNGetSpaceResult result, PNStatus status) {
            if (!status.isError()) {
                PNSpace space = result.getSpace(); // the requested space object
                Object custom = space.getCustom(); // the requested custom payload (if any)
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

### Response

The `getSpace()` operation returns a `PNGetSpaceResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `getSpace()` |    `PNSpace`    | Returns the requested space.|

## GET SPACES

### Description
Returns a paginated list of space objects, optionally including each space's custom data object.

### Method(s)

To `Get spaces` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.getSpaces();
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `includeFields` |   `PNSpaceFields[]`   |   No    | Array of additional/complex space attributes to include in response. |
| `limit` |    `Integer`  |   No    | Number of spaces to return in response. Default is 100, which is also the maximum value.|
| `start` |   `String`   |   No    | Previously-returned cursor bookmark for fetching the next page.|
| `end` | `String` | No | Previously-returned cursor bookmark for fetching the previous page. Ignored if you also supply the start parameter. |
| `withTotalCount` | `Boolean` | No | Request totalCount to be included in paginated response. By default, totalCount is omitted.|
| `sync` | `PNGetSpacesResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNGetSpacesResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Get spaces

```java
pubnub.getSpaces()
    .async(new PNCallback<PNGetSpacesResult> () {
        @Override
        public void onResponse(PNGetSpacesResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNSpace> spaces = result.getData(); // the requested space list
                result.getNext(); // cursor bookmark for the next page
                result.getPrev(); // cursor bookmark for the previous page result
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get spaces with its custom data

```java
pubnub.getSpaces()
    .async(new PNCallback<PNGetSpacesResult> () {
        @Override
        public void onResponse(PNGetSpacesResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNSpace> spaces = result.getData(); // the requested spaces
                for (PNSpace space: spaces) {
                    space.getCustom(); // space's custom data (if any)
                }
                result.getNext(); // cursor bookmark for the next page
                result.getPrev(); // cursor bookmark for the previous page result
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get spaces with recursive paging

```java
void main() {
    fetchSpaces(null, new FetchCallback() {
        @Override
        public void done(PNGetSpacesResult result) {
            List<PNSpace> spaces = result.getData(); // the requested spaces
        }
    });
}

interface FetchCallback {

    void done(PNGetSpacesResult result);
}

/**
 * Fetches 10 spaces at a time, recursively and in a paged manner.
 *
 * @param next     The cursor bookmark for the next page.
 * @param callback The callback to dispatch fetched spaces to.
 */
void fetchSpaces(String next, FetchCallback callback) {
    GetSpaces getSpacesBuilder = pubnub.getSpaces()
        .includeFields(PNSpaceFields.CUSTOM)
        .limit(10)
        .withTotalCount(true);

    if (next != null)
        getSpacesBuilder.start(next);

    getSpacesBuilder.async(new PNCallback<PNGetSpacesResult> () {
        @Override
        public void onResponse(PNGetSpacesResult result, PNStatus status) {
            if (!status.isError()) {
                if (!(result.getData().isEmpty())) {
                    callback.done(result);
                    fetchSpaces(result.getNext(), callback);
                }
            }
        }
    });
}
```

### Response

The `getSpaces()` operation returns a `PNGetSpacesResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `getData()` |    `List<PNSpace>`    | Returns the requested space list.|
| `getNext()` |    `String`    | Returns cursor bookmark for fetching the next page.|
| `getPrev()` |    `String`    | Returns cursor bookmark for fetching the previous page.|
| `getTotalCount()` |    `Integer`    | Returns the total count of objects without pagination.|

## UPDATE SPACE

### Description
Updates a space with the specified properties. Returns the updated space object, optionally including the space's custom data object.

### Method(s)

To `Update a space` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.updateSpace().space(pnSpace);
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `space` |   `PNSpace`   |   No    | The space to update. |
| `includeFields` |   `PNSpaceFields[]`   |   No    | Array of additional/complex space attributes to include in response.|
| `sync` | `PNUpdateSpaceResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNUpdateSpaceResult>` | No | Executes the call asynchronously. |

> **NOTES:**
>
>- You can change all of the space object's properties, except its ID.
>
>- Invalid property names are silently ignored and will not cause a request to fail.
>
>- If you update the `custom` property, you must completely replace it; partial updates are not supported.

### Basic usage

#### Update space

```java
PNSpace pnSpace = new PNSpace("space_1", "foo"); // assume this space is already created
pnSpace.setEmail("foo@bar.com"); // set a new email address

pubnub.updateSpace()
    .space(pnSpace)
    .async(new PNCallback<PNUpdateSpaceResult> () {
        @Override
        public void onResponse(PNUpdateSpaceResult result, PNStatus status) {
            if (!status.isError()) {
                PNSpace space = result.getSpace(); // the updated space object
                System.out.println(space.getEmail()); // prints foo@bar.com
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get spaces with its custom data

```java
PNSpace pnSpace = new PNSpace("space_1", "foo"); // assume this space is already created
pnSpace.setEmail("foo@bar.com"); // set a new email address

// set another custom object
HashMap <String, Object> customSpaceMap = new HashMap<> ();
customSpaceMap.put("city", "SF");
customSpaceMap.put("limit", 30);
pnSpace.setCustom(customSpaceMap);

pubnub.updateSpace()
    .includeFields(PNSpaceFields.CUSTOM)
    .space(pnSpace)
    .async(new PNCallback<PNUpdateSpaceResult> () {
        @Override
        public void onResponse(PNUpdateSpaceResult result, PNStatus status) {
            if (!status.isError()) {
                PNSpace space = result.getSpace(); // the updated space object
                space.getCustom(); // space's custom data
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

### Response

The `updateSpace()` operation returns a `PNUpdateSpaceResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `updateSpace()` |    `PNSpace`    | Returns the updated space.|

## DELETE SPACE

### Description
Deletes the specified space object.

### Method(s)

To `Delete a space` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.deleteSpace().space("space_1");
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `spaceId` |    `String`  |   Yes    | ID of the space to delete.  |
| `sync` | `PNDeleteSpaceResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNDeleteSpaceResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Delete a space

```java
pubnub.deleteSpace()
    .spaceId("space_1")
    .async(new PNCallback<PNDeleteSpaceResult> () {
        @Override
        public void onResponse(PNDeleteSpaceResult result, PNStatus status) {
            if (!status.isError()) {
                // no actionable data
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }

        }
    });
```

### Response

The `deleteSpace()` operation returns a `PNDeleteSpaceResult` object which has no actionable data.