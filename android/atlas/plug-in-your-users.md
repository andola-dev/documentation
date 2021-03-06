# Add your users to Atlas

![](atlas-android-participants.png)

Layer recognizes that you might already have a User Model in your app.  Atlas can work with any existing user management system - all you have to is implement the `Participant` interface. You can create your class, implement `Atlas.Participant`, and add any other relevant fields (such as avatar image, username, phone number, etc).

```java
public class Participant implements Atlas.Participant {
    public String userId;
    public String firstName;
    public String lastName;
    public Bitmap avatarImg;

    public String getId() {
        return userId;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public Bitmap getAvatarImage(){
        return avatarImg;
    }
}
```

You will also need to implement the `Atlas.ParticipantProvider` interface in order to allow the authenticated user to start conversations with other users:

```java
public class ParticipantProvider implements Atlas.ParticipantProvider {

    private final Map<String, Participant> mParticipantMap = 
        new HashMap<String, Participant>();

    public void refresh() {
        //Connect to your user management service and sync the user's 
        // contact list, making sure you include the authenticated user. 
        // Then, store those users in the participant map
        /*
        //Add the authenticated user
        Participant authUser = new Participant();
        authUser.userId = currUser.userId;
        authUser.firstName = currUser.firstName;
        authUser.lastName = currUser.lastName;
        authUser.avatarImg = currUser.getAvatarImage();

        mParticipantMap.put(authUser.getId(), authUser);

        //Add all friends in the user's contact list
        foreach (Contact c : contactList) {
            Participant p = new Participant();
            p.userId = c.userId;
            p.firstName = c.firstName;
            p.lastName = c.lastName;
            p.avatarImg = c.getAvatarImage();

            mParticipantMap.put(p.getId(), p);
        }
        */
    }

    public Map<String, Atlas.Participant> getParticipants(String filter, Map<String, 
                                                           Atlas.Participant> result) {
        if (result == null) {
            result = new HashMap<String, Atlas.Participant>();
        }

        if (filter == null) {
            for (Participant p : mParticipantMap.values()) {
                result.put(p.getId(), p);
            }
            return result;
        }

        for (Participant p : mParticipantMap.values()) {
            if (p.firstName != null && !p.firstName.toLowerCase().contains(filter) && 
                p.lastName != null && !p.lastName.toLowerCase().contains(filter)) {
                result.remove(p.getId());
                continue;
            }

            result.put(p.getId(), p);
        }

        return result;
    }

    @Override
    public Atlas.Participant getParticipant(String userId) {
        return get(userId);
    }
}
```

When you're done, you will see a list of your users in the participant picker UI element.