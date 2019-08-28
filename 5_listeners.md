# LISTENER CHANGES

```java
pubnub.addListener(new SubscribeCallback() {

    // existing 4 callbacks omitted for the sake of simplicity

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