# MEMBERS

## INTRODUCTION

In the Java SDK, and in terms of the Objects API, a **Member** is represented as an instance of `PNMember`. A `PNMember` can't be instantiated, as it's created indirectly in different scenarios (e.g. when getting members from PubNub). A `PNMember` is just a wrapper around `PNUser`.

### Complete overview

#### Getters

| Method           |    Return type    | Description  |
| ---------------- | ---------- | ------------ |
| `getUser()` |    `PNUser`    | Returns the belonging user.|
| `getCustom()` |    `Object`    | Returns the members's deserializable custom data.|
| `getCreated()` |    `String`    | Returns the date and time the object was created.|
| `getUpdated()` |    `String`    | Returns the date and time the object was updated.|
| `getETag()` |    `String`    | Returns the the object's content fingerprint used in conditional requests.|

However, to manage members on your own and to avoid creating ad-hoc json data, a tiny helper class is available to do so: `Member`. It's a easy-to-use wrapper class:

```java
Member memberFoo = Member.userId("foo"); // instantiates a Member

Member memberBar = Member.userId("bar").custom(new HashMap<String, Object> () {
    {
        put("starred", true);
    }
}); // instantiates a Member and also sets its custom data
```

## GET MEMBERS

### Description

Get the specified space's member users.

### Method(s)

To `Get members` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.getMembers().spaceId("space_1");
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `spaceId` |    `String`  |   Yes    | ID of the space whose members you wish to retrieve. |
| `includeFields` |   `PNMemberFields[]`   |   No    | List of additional/complex attributes to include in response. |
| `limit` |    `Integer`  |   No    | Number of members to return in response.|
| `start` |   `String`   |   No    | Previously-returned cursor bookmark for fetching the next page.|
| `end` | `String` | No | Previously-returned cursor bookmark for fetching the previous page. Ignored if you also supply the start parameter. |
| `withTotalCount` | `Boolean` | No | Request totalCount to be included in paginated response. By default, totalCount is omitted.|
| `sync` | `PNGetMembersResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNGetMembersResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Get members

```java
pubnub.getMembers()
    .spaceId("space_1")
    .async(new PNCallback<PNGetMembersResult> () {
        @Override
        public void onResponse(PNGetMembersResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMember> memberList = result.getData(); // requested member list
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get members with their custom data

```java
pubnub.getMembers()
    .spaceId("space_1")
    .includeFields(PNMemberFields.CUSTOM, PNMemberFields.USER, PNMemberFields.USER_CUSTOM)
    .async(new PNCallback<PNGetMembersResult> () {
        @Override
        public void onResponse(PNGetMembersResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMember> memberList = result.getData(); // requested member list
                for (PNMember pnMember: memberList) {
                    pnMember.getCustom(); // requested via PNMemberFields.CUSTOM
                    pnMember.getUser(); // requested via PNMemberFields.USER
                    pnMember.getUser().getCustom(); // requested via PNMemberFields.USER_CUSTOM
                }
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get members with recursive paging

```java
void main() {
    fetchMembers(null, new FetchCallback() {
        @Override
        public void done(PNGetMembersResult result) {
            List<PNMember> members = result.getData(); // the requested members
        }
    });
}

interface FetchCallback {

    void done(PNGetMembersResult result);
}

/**
 * Fetches 10 members at a time, recursively and in a paged manner.
 *
 * @param next     The cursor bookmark for the next page.
 * @param callback The callback to dispatch fetched members to.
 */
void fetchMembers(String next, FetchCallback callback) {
    GetMembers getMembersBuilder = pubnub.getMembers()
        .limit(10)
        .withTotalCount(true);

    if (next != null)
        getMembersBuilder.start(next);

    getMembersBuilder.async(new PNCallback<PNGetMembersResult> () {
        @Override
        public void onResponse(PNGetMembersResult result, PNStatus status) {
            if (!status.isError()) {
                if (!(result.getData().isEmpty())) {
                    callback.done(result);
                    fetchMembers(result.getNext(), callback);
                }
            }
        }
    });
}
```

### Response

The `getMembers()` operation returns a `PNGetMembersResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `getData()` |    `List<PNMember>`    | Returns the requested members list.|
| `getNext()` |    `String`    | Returns cursor bookmark for fetching the next page.|
| `getPrev()` |    `String`    | Returns cursor bookmark for fetching the previous page.|
| `getTotalCount()` |    `Integer`    | Returns the total count of objects without pagination.|

## MANAGE MEMBERS

### Description

The Java SDK features a single method for managing members. Managing members include adding them to a space, updating existing members or removing them from a space.

These operations can be combined. To `Manage members` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.manageMembers().spaceId("space_1");
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `spaceId` |    `String`  |   Yes    | ID of the space whose members you wish to manage (add, update or remove). |
| `add` |   `Member[]`   |   No    | Array of members to add to the space, with optional custom data for each.|
| `update` |   `Member[]`   |   No    | Array of members indicating the complete list of user(s) who should belong to the space, along with optional custom data.|
| `remove` |   `Member[]`   |   No    | Array of members to be removed from the space. |
| `includeFields` |   `PNMemberFields[]`   |   No    | Array of additional/complex attributes to include in response. |
| `limit` |    `Integer`  |   No    | Number of members to return in response.|
| `start` |   `String`   |   No    | Previously-returned cursor bookmark for fetching the next page.|
| `end` | `String` | No | Previously-returned cursor bookmark for fetching the previous page. Ignored if you also supply the start parameter. |
| `withTotalCount` | `Boolean` | No | Request totalCount to be included in paginated response. By default, totalCount is omitted.|
| `sync` | `PNManageMembersResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNManageMembersResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Add members

```java
pubnub.manageMembers()
    .spaceId("space_1")
    .add(Member.userId("user_1"))
    .async(new PNCallback<PNManageMembersResult> () {
        @Override
        public void onResponse(PNManageMembersResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMember> members = result.getData(); // list of members currently in the requested space
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Add members with custom data

```java
pubnub.manageMembers()
    .spaceId("space_1")
    .add(Member.userId("user_1").custom(new HashMap<String, Object> () {  // add custom data
        {
            put("color", "yellow");
            put("private", true);
            put("stars", 25);
        }
    }))
    .includeFields(PNMemberFields.CUSTOM) // let's retreive it too
    .async(new PNCallback<PNManageMembersResult> () {
        @Override
        public void onResponse(PNManageMembersResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMember> members = result.getData(); // list of members currently in the requested space
                for (PNMember member: members) {
                    member.getCustom(); // requested via PNMemberFields.CUSTOM (if any)
                }
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Update members

```java
pubnub.manageMembers()
    .spaceId("space_1")
    .update(Member.userId("user_1"),
            Member.userId("user_2").custom(new HashMap<String, Object> () {
            {
                put("color", "green");
                put("starred", false);
            }
            }),
            Member.userId("user_3"))
    .async(new PNCallback<PNManageMembersResult> () {
        @Override
        public void onResponse(PNManageMembersResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMember> members = result.getData(); // list of members currently in the requested space
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Remove members

```java
pubnub.manageMembers()
    .spaceId("space_1")
    .remove(Member.userId("user_1"),
            Member.userId("user_2"),
            Member.userId("user_3"))
    .async(new PNCallback<PNManageMembersResult> () {
        @Override
        public void onResponse(PNManageMembersResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNMember> members = result.getData(); // list of members currently in the requested space
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

### Response

The `manageMembers()` operation returns a `PNManageMembersResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `getData()` |    `List<PNMember>`    | Returns the current member list.|
| `getNext()` |    `String`    | Returns cursor bookmark for fetching the next page.|
| `getPrev()` |    `String`    | Returns cursor bookmark for fetching the previous page.|
| `getTotalCount()` |    `Integer`    | Returns the total count of objects without pagination.|