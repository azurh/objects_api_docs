# MEMBERSHIPS

## INTRODUCTION

In the Java SDK, and in terms of the Objects API, a **Membership** is represented as an instance of `PNMembership`. A `PNMembership` can't be instantiated, as it's created indirectly in different scenarios (e.g. when getting memberships from PubNub). A `PNMembership` is just a wrapper around `PNSpace`.

### Complete overview

#### Getters

| Method           |    Return type    | Description  |
| ---------------- | ---------- | ------------ |
| `getSpace()` |    `PNSpace`    | Returns the belonging space.|
| `getCustom()` |    `Object`    | Returns the memberships' deserializable custom data.|
| `getCreated()` |    `String`    | Returns the date and time the object was created.|
| `getUpdated()` |    `String`    | Returns the date and time the object was updated.|
| `getETag()` |    `String`    | Returns the the object's content fingerprint used in conditional requests.|

However, to manage memberships on your own and to avoid creating ad-hoc json data, a tiny helper class is available to do so: `Membership`. It's a easy-to-use wrapper class:

```java
Membership membershipFoo = Membership.spaceId("foo"); // instantiates a Membership

HashMap<String, Object> customMap = new HashMap<>();
customMap.put("starred", true);
Membership membershipBar = Membership.spaceId("bar").custom(customMap); // instantiates a Membership and also sets its custom data
```

## GET MEMBERSHIPS

### Description

Get the specified user's membership spaces.

### Method(s)

To `Get memberships` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.getMemberships().userId("user_1");
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `userId` |    `String`  |   Yes    | ID of the user whose memberships you wish to retrieve. |
| `includeFields` |   `PNMembershipFields[]`   |   No    | List of additional/complex attributes to include in response. |
| `limit` |    `Integer`  |   No    | Number of memberships to return in response.|
| `start` |   `String`   |   No    | Previously-returned cursor bookmark for fetching the next page.|
| `end` | `String` | No | Previously-returned cursor bookmark for fetching the previous page. Ignored if you also supply the start parameter. |
| `withTotalCount` | `Boolean` | No | Request totalCount to be included in paginated response. By default, totalCount is omitted.|
| `sync` | `PNGetMembershipsResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNGetMembershipsResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Get memberships

```java
pubnub.getMemberships()
    .userId("user_1")
    .async(new PNCallback<PNGetMembershipsResult> () {
        @Override
        public void onResponse(PNGetMembershipsResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMembership> membershipList = result.getData(); // requested membership list
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get memberships with their custom data

```java
pubnub.getMemberships()
    .userId("user_1")
    .includeFields(PNMembershipFields.CUSTOM, PNMembershipFields.SPACE, PNMembershipFields.SPACE_CUSTOM)
    .async(new PNCallback<PNGetMembershipsResult> () {
        @Override
        public void onResponse(PNGetMembershipsResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMembership> membershipList = result.getData(); // requested membership list
                for (PNMembership pnMembership: membershipList) {
                    pnMembership.getCustom(); // requested via PNMembershipFields.CUSTOM
                    pnMembership.getSpace(); // requested via PNMembershipFields.SPACE
                    pnMembership.getSpace().getCustom(); // requested via PNMembershipFields.SPACE_CUSTOM
                }
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get memberships with recursive paging

```java
void main() {
    fetchMemberships(null, new FetchCallback() {
        @Override
        public void done(PNGetMembershipsResult result) {
            List<PNMembership> memberships = result.getData(); // the requested memberships
        }
    });
}

interface FetchCallback {

    void done(PNGetMembershipsResult result);
}

/**
 * Fetches 10 membersips at a time, recursively and in a paged manner.
 *
 * @param next     The cursor bookmark for the next page.
 * @param callback The callback to dispatch fetched memberships to.
 */
void fetchMemberships(String next, FetchCallback callback) {
    GetMemberships getMembershipsBuilder = pubnub.getMemberships()
        .limit(10)
        .withTotalCount(true);

    if (next != null)
        getMembershipsBuilder.start(next);

    getMembershipsBuilder.async(new PNCallback<PNGetMembershipsResult> () {
        @Override
        public void onResponse(PNGetMembershipsResult result, PNStatus status) {
            if (!status.isError()) {
                if (!(result.getData().isEmpty())) {
                    callback.done(result);
                    fetchMemberships(result.getNext(), callback);
                }
            }
        }
    });
}
```

### Response

The `getMemberships()` operation returns a `PNGetMembershipsResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `getData()` |    `List<PNMembership>`    | Returns the requested memberships list.|
| `getNext()` |    `String`    | Returns cursor bookmark for fetching the next page.|
| `getPrev()` |    `String`    | Returns cursor bookmark for fetching the previous page.|
| `getTotalCount()` |    `Integer`    | Returns the total count of objects without pagination.|

## MANAGE MEMBERSHIPS

### Description

The Java SDK features a single method for managing memberships. Managing memberships include adding users to them, updating existing memberships or removing users from them.

These operations can be combined. To `Manage memberships` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.manageMemberships().userId("user_1");
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `userId` |    `String`  |   Yes    | ID of the user whose memberships you wish to manage (add, update or remove). |
| `add` |   `Membership[]`   |   No    | Array of objects indicating the space(s) to be joined, along with optional custom data.|
| `update` |   `Membership[]`   |   No    | Array of objects indicating the complete list of space(s) to which the user should belong, along with optional custom data.|
| `remove` |   `Membership[]`   |   No    | Array of spaces from which to remove the user.|
| `includeFields` |   `PNMembershipFields[]`   |   No    | Array of additional/complex attributes to include in response. |
| `limit` |    `Integer`  |   No    | Number of memberships to return in response.|
| `start` |   `String`   |   No    | Previously-returned cursor bookmark for fetching the next page.|
| `end` | `String` | No | Previously-returned cursor bookmark for fetching the previous page. Ignored if you also supply the start parameter. |
| `withTotalCount` | `Boolean` | No | Request totalCount to be included in paginated response. By default, totalCount is omitted.|
| `sync` | `PNManageMembershipsResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNManageMembershipsResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Add memberships

```java
pubnub.manageMemberships()
    .userId("user_1")
    .add(Membership.spaceId("space_1"))
    .async(new PNCallback<PNManageMembershipsResult> () {
        @Override
        public void onResponse(PNManageMembershipsResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMembership> memberships = result.getData(); // list of memberships currently in the requested space
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Add memberships with custom data

```java
HashMap<String, Object> customMap = new HashMap<>();
customMap.put("color", "yellow");
customMap.put("private", true);
customMap.put("stars", 25);

pubnub.manageMemberships()
    .userId("user_1")
    .add(Membership.spaceId("space_1").custom(customMap))
    .includeFields(PNMembershipFields.CUSTOM) // let's retreive it too
    .async(new PNCallback<PNManageMembershipsResult> () {
        @Override
        public void onResponse(PNManageMembershipsResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMembership> memberships = result.getData(); // list of memberships currently in the requested space
                for (PNMembership membership: memberships) {
                    membership.getCustom(); // requested via PNMembershipFields.CUSTOM (if any)
                }
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Update memberships

```java
HashMap<String, Object> customMap = new HashMap<>();
customMap.put("color", "green");
customMap.put("starred", false);

pubnub.manageMemberships()
    .userId("user_1")
    .update(Membership.spaceId("space_1"),
            Membership.spaceId("space_2").custom(customMap),
            Membership.spaceId("space_3"))
    .async(new PNCallback<PNManageMembershipsResult> () {
        @Override
        public void onResponse(PNManageMembershipsResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMembership> memberships = result.getData(); // list of memberships currently in the requested space
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Remove memberships

```java
pubnub.manageMemberships()
    .userId("user_1")
    .remove(Membership.spaceId("space_1"),
            Membership.spaceId("space_2"),
            Membership.spaceId("space_3"))
    .async(new PNCallback<PNManageMembershipsResult> () {
        @Override
        public void onResponse(PNManageMembershipsResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMembership> memberships = result.getData(); // list of memberships currently in the requested space
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

### Response

The `manageMemberships()` operation returns a `PNManageMembershipsResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `getData()` |    `List<PNMembership>`    | Returns the current membership list.|
| `getNext()` |    `String`    | Returns cursor bookmark for fetching the next page.|
| `getPrev()` |    `String`    | Returns cursor bookmark for fetching the previous page.|
| `getTotalCount()` |    `Integer`    | Returns the total count of objects without pagination.|