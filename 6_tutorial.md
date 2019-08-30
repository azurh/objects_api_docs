# Java V4 Objects (BETA)

#### EVENT LISTENERS

Once you have subscribed to user and space channels, the client will receive events for user, space and membership updates. Add listeners to receive events for each resource.

```java
pubnub.addListener(new SubscribeCallback() {

    // other callbacks omitted for the sake of simplicity
    
    @Override
    public void user(PubNub pubnub, PNUserResult pnUserResult) {
        // for Objects, this will trigger when:
        // . user updated
        // . user deleted
        PNUser pnUser = pnUserResult.getUser(); // the user for which the event applies to
        pnUserResult.getEvent(); // the event name
    }

    @Override
    public void space(PubNub pubnub, PNSpaceResult pnSpaceResult) {
        // for Objects, this will trigger when:
        // . space updated
        // . space deleted
        PNSpace pnSpace = pnSpaceResult.getSpace(); // the space for which the event applies to
        pnSpaceResult.getEvent(); // the event name
    }

    @Override
    public void membership(PubNub pubnub, PNMembershipResult pnMembershipResult) {
        // for Objects, this will trigger when:
        // . user added to a space
        // . user removed from a space
        // . membership updated on a space
        JsonElement data = pnMembershipResult.getData(); // membership data for which the event applies to
        pnMembershipResult.getEvent(); // the event name
    }
})
```

#### EVENT FORMATS

> **Note for docs team:** Please remove the "Event formats" section as seen in the Javascript docs, since they don't apply to the Java SDK. Tehcnically they are the same, but Java SDK has everything deserialized into real classes, so developers don't have to bother with raw json and as such, the table from the Javascript docs is irrelevant to them.

| Method           |    Values    | Note |
| ---------------- | ---------- |---------- |
| `getEvent()` |    `String`    | Applies to all. Returns the event name. Could be "create", "update" or "delete".|
| `getChannel()` |    `String`    | Applies to all. Returns the channel name which the event was published to.|
| `getPublisher()` |    `String`    | Applies to all. Returns the UUID of the user who generated the event.|
| `getTimetoken()` |    `Long`    | Applies to all. Returns the timetoken of when the event was created.|
| `getUser()`  |    `PNUser`    | Only for `PNUserResult`. Returns the user for which the event applies to.|
| `getSpace()` |    `PNSpace`    | Only for `PNSpaceResult`. Returns the space for which the event applies to.|
| `getData()` |    `JsonElement`    | Only for `PNMembershipResult`. Returns the membership for which the event applies to.|

#### SAMPLE EVENTS

> **Note for docs team:** Please remove the "Sample events" section as seen in the Javascript docs, since they don't apply to the Java SDK. The reason is the same as described in the [event formats section](#event-formats). Nobody should be interested in how a JSON looks like unless you have everything deserialized, which is exaclty what we do with the Java SDK.


## The rest remains the same for the Java SDK tutorial
