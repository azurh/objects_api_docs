# USERS

## INTRODUCTION

In the Java SDK, and in terms of the Objects API, a **User** is represented as an instance of `PNUser`. A `PNUser` is instantiated as follows:

```java
PNUser pnUser = new PNUser("user_1", "foo");
```

The user's `id` and `name` are mandatory and as such need to be passed directly to the constructor.

Other properties (except the `id`) are settable via setters:

```java
pnUser.setEmail("foo@bar.com");
pnUser.setExternalId("user_foo");
pnUser.setCustom(new HashMap<String, Object>() {{
    put("color", "red");
    put("role", "student");
}});
```

The `PNUser` class features other properties too, which are not settable directly. Instead, the server is responsible for them and assigns values in different scenarios (e.g. when getting a user from PubNub).

```java
pnUser.getCreated(); // user creation date
pnUser.getUpdated(); // user update date
pnUser.getETag();    // etag
```

### Complete overview

#### Getters
| Method           |    Return type    | Description  |
| ---------------- | ---------- | ------------ |
| `getId()` |    `String`    | Returns the user's unique identifier.|
| `getName()` |    `String`    | Returns the user's name.|
| `getEmail()` |    `String`    | Returns the user's email.|
| `getExternalId()` |    `String`    | Returns the user's external identifier.|
| `getProfileUrl()` |    `String`    | Returns the the URL of the user's profile picture.|
| `getCustom()` |    `Object`    | Returns the user's deserializable custom data.|
| `getCreated()` |    `String`    | Returns the date and time the object was created.|
| `getUpdated()` |    `String`    | Returns the date and time the object was updated.|
| `getETag()` |    `String`    | Returns the the object's content fingerprint used in conditional requests.|

#### Setters

| Method |    Parameter Type | Description |
| --------- | ------- |------------ |
| `PNUser()` |`String, String`|Creates a new instance of `PNUser` with `id` and `name` provided.|
| `setName()` |`String`|Sets the name of the user.|
| `setEmail()` |`String`| Sets a personal email address.|
| `setExternalId()` |`String`| Sets a user's identifier in an external system.|
| `setProfileUrl()` |`String`| Sets a user's profile picture URL.|
| `setCustom()` |`Object`| Sets a user's custom data. Serialization is done automatically, but the end result must be a Json object with scalar values only.|

> **NOTE**: The setters are chainable and offer a fluent API as such.

## CREATE USER

### Description

Creates a user with the specified properties. Returns the created user object, optionally including the user's custom data object.

### Method(s)

To `Create a user` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.createUser().user(pnUser);
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `user` |    `PNUser`  |   Yes    | The user you wish to create.|
| `includeFields` |   `PNUserFields[]`   |   No    | Array of additional/complex user attributes to include in response.|
| `sync` | `PNCreateUserResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNCreateUserResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Create a user

```java
PNUser pnUser = new PNUser("user_1", "foo");

pubnub.createUser()
    .user(pnUser)
    .async(new PNCallback<PNCreateUserResult>() {
        @Override
        public void onResponse(PNCreateUserResult result, PNStatus status) {
            if (!status.isError()) {
                PNUser user = result.getUser(); // the created user object
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Create a user and retreive its custom data

```java
PNUser pnUser = new PNUser("user_1", "foo");

JsonObject customData = new JsonObject();
customData.addProperty("color", "red");
customData.addProperty("city", "SF");

pnUser.setCustom(customData);

pubnub.createUser()
    .user(pnUser)
    .includeFields(PNUserFields.CUSTOM)
    .async(new PNCallback<PNCreateUserResult> () {
        @Override
        public void onResponse(PNCreateUserResult result, PNStatus status) {
            if (!status.isError()) {
                PNUser user = result.getUser(); // the created user object
                Object custom = user.getCustom(); // the requested custom payload (if any)
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

### Response

The `createUser()` operation returns a `PNCreateUserResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `getUser()` |    `PNUser`    | Returns the created user.|

## GET USER

### Description

Returns the specified user object, optionally including the user's custom data object.

### Method(s)

To `Get a user` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.getUser().userId("user_1");
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `userId` |    `String`  |   Yes    | The ID of the user to retrieve. |
| `includeFields` |   `PNGetUserResult`   |   No    | Additional JSON fields to be returned in the response. |
| `sync` | `PNGetUserResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNGetUserResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Get a user

```java
pubnub.getUser()
    .userId("user_1")
    .async(new PNCallback<PNGetUserResult> () {
        @Override
        public void onResponse(PNGetUserResult result, PNStatus status) {
            if (!status.isError()) {
                PNUser user = result.getUser(); // the requested user object
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get a user with its custom data

```java
pubnub.getUser()
    .userId("user_1")
    .includeFields(PNUserFields.CUSTOM)
    .async(new PNCallback<PNGetUserResult> () {
        @Override
        public void onResponse(PNGetUserResult result, PNStatus status) {
            if (!status.isError()) {
                PNUser user = result.getUser(); // the requested user object
                Object custom = user.getCustom(); // the requested custom payload (if any)
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

### Response

The `getUser()` operation returns a `PNGetUserResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `getUser()` |    `PNUser`    | Returns the requested user.|

## GET USERS

### Description
Returns a paginated list of user objects, optionally including each user's custom data object.

### Method(s)

To `Get users` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.getUsers();
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `includeFields` |   `PNUserFields[]`   |   No    | Array of additional/complex user attributes to include in response. |
| `limit` |    `Integer`  |   No    | Number of users to return in response.|
| `start` |   `String`   |   No    | Previously-returned cursor bookmark for fetching the next page.|
| `end` | `String` | No | Previously-returned cursor bookmark for fetching the previous page. Ignored if you also supply the start parameter. |
| `withTotalCount` | `Boolean` | No | Request totalCount to be included in paginated response. By default, totalCount is omitted.|
| `sync` | `PNGetUsersResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNGetUsersResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Get users

```java
pubnub.getUsers()
    .async(new PNCallback<PNGetUsersResult> () {
        @Override
        public void onResponse(PNGetUsersResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNUser> users = result.getData(); // the requested user list
                result.getNext(); // cursor bookmark for the next page
                result.getPrev(); // cursor bookmark for the previous page result
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get users with its custom data

```java
pubnub.getUsers()
    .async(new PNCallback<PNGetUsersResult> () {
        @Override
        public void onResponse(PNGetUsersResult result, PNStatus status) {
            if (!status.isError()) {
                List<PNUser> users = result.getData(); // the requested users
                for (PNUser user: users) {
                    user.getCustom(); // user's custom data (if any)
                }
                result.getNext(); // cursor bookmark for the next page
                result.getPrev(); // cursor bookmark for the previous page result
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get users with recursive paging

```java
void main() {
    fetchUsers(null, new FetchCallback() {
        @Override
        public void done(PNGetUsersResult result) {
            List<PNUser> users = result.getData(); // the requested users
        }
    });
}

interface FetchCallback {

    void done(PNGetUsersResult result);
}

/**
 * Fetches 10 users at a time, recursively and in a paged manner.
 *
 * @param next     The cursor bookmark for the next page.
 * @param callback The callback to dispatch fetched users to.
 */
void fetchUsers(String next, FetchCallback callback) {
    GetUsers getUsersBuilder = pubnub.getUsers()
        .includeFields(PNUserFields.CUSTOM)
        .limit(10)
        .withTotalCount(true);

    if (next != null)
        getUsersBuilder.start(next);

    getUsersBuilder.async(new PNCallback<PNGetUsersResult> () {
        @Override
        public void onResponse(PNGetUsersResult result, PNStatus status) {
            if (!status.isError()) {
                if (!(result.getData().isEmpty())) {
                    callback.done(result);
                    fetchUsers(result.getNext(), callback);
                }
            }
        }
    });
}
```

### Response

The `getUsers()` operation returns a `PNGetUsersResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `getData()` |    `List<PNUser>`    | Returns the requested user list.|
| `getNext()` |    `String`    | Returns cursor bookmark for fetching the next page.|
| `getPrev()` |    `String`    | Returns cursor bookmark for fetching the previous page.|
| `getTotalCount()` |    `Integer`    | Returns the total count of objects without pagination.|

## UPDATE USER

### Description
Updates a user with the specified properties. Returns the updated user object, optionally including the user's custom data object.

### Method(s)

To `Update a user` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.updateUser().user(pnUser);
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `user` |   `PNUser`   |   No    | The user to update. |
| `includeFields` |   `PNUserFields[]`   |   No    | Array of additional/complex user attributes to include in response.|
| `sync` | `PNUpdateUserResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNUpdateUserResult>` | No | Executes the call asynchronously. |

> **NOTES:**
>
>- You can change all of the user object's properties, except its ID.
>
>- Invalid property names are silently ignored and will not cause a request to fail.
>
>- If you update the `custom` property, you must completely replace it; partial updates are not supported.

### Basic usage

#### Update user

```java
PNUser pnUser = new PNUser("user_1", "foo"); // assume this user is already created
pnUser.setEmail("foo@bar.com"); // set a new email address

pubnub.updateUser()
    .user(pnUser)
    .async(new PNCallback<PNUpdateUserResult> () {
        @Override
        public void onResponse(PNUpdateUserResult result, PNStatus status) {
            if (!status.isError()) {
                PNUser user = result.getUser(); // the updated user object
                System.out.println(user.getEmail()); // prints foo@bar.com
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

#### Get users with its custom data

```java
PNUser pnUser = new PNUser("user_1", "foo"); // assume this user is already created
pnUser.setEmail("foo@bar.com"); // set a new email address
pnUser.setCustom(new HashMap <String Object > () {
    {
        put("city", "SF");
        put("age", "22");
    }
}); // set another custom object

pubnub.updateUser()
    .includeFields(PNUserFields.CUSTOM)
    .user(pnUser)
    .async(new PNCallback<PNUpdateUserResult> () {
        @Override
        public void onResponse(PNUpdateUserResult result, PNStatus status) {
            if (!status.isError()) {
                PNUser user = result.getUser(); // the updated user object
                user.getCustom(); // user's custom data
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }
        }
    });
```

### Response

The `updateUser()` operation returns a `PNUpdateUserResult` object which contains the following operations:

| Method           |    Type    | Description  |
| ---------------- | ---------- | ------------ |
| `updateUser()` |    `PNUser`    | Returns the updated user.|

## DELETE USER

### Description
Deletes the specified user object.

### Method(s)

To `Delete a user` you can use the following method(s) in the Java V4 SDK:

```java
this.pubnub.deletUser().user("user_1");
```

| Parameter |    Type    | Required | Description |
| --------- | ---------- | -------- | ----------- |
| `userId` |    `String`  |   Yes    | ID of the user to delete. |
| `sync` | `PNDeleteUserResult` | No | Executes the call. Blocks the thread, exception is thrown if something goes wrong. |
| `async` | `PNCallback<PNDeleteUserResult>` | No | Executes the call asynchronously. |

### Basic usage

#### Delete a user

```java
pubnub.deleteUser()
    .userId("user_1")
    .async(new PNCallback<PNDeleteUserResult> () {
        @Override
        public void onResponse(PNDeleteUserResult result, PNStatus status) {
            if (!status.isError()) {
                // no actionable data
            } else {
                status.getErrorData().getThrowable().printStackTrace();
            }

        }
    });
```

### Response

The `deleteUser()` operation returns a `PNDeleteUserResult` object which has no actionable data.