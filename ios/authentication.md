# Authentication

## Introduction

Chat21 uses Firebase as backend \(standalone under development\), and because of this dependency Chat21 actually relies on Firebase for authentication and user management.

All of these examples are available in the **Chat21 Swift Playground** project 👉🏻 [https://github.com/chat21/chat21-swift-playground](https://github.com/chat21/chat21-swift-playground). We strongly recommend you to install and configure the playground and use it to try all these examples.

If you have feedbacks or suggestions about this documentation, please send us an email at **support@frontiere21.it**. We are always open to improvements coming from Chat21 Community!

## Register a new user

To register a new user using email and password you can use directly Chat21 **ChatAuth** class method _createUser_, like in the example below:

```swift
let email = "MY EMAIL"; /** FIREBASE USER EMAIL **/
let password = "MY PASSWORD"; /** FIREBASE USER PASSWORD **/
ChatAuth.createUser(withEmail: email, password: password) { (user, error) in
    if let user = user {
        user.firstname = "FIRST NAME";
        user.lastname = "LAST NAME";
        let chatm = ChatManager.getInstance()
        // always save the new user as a contact in remote contacts DB
        chatm?.createContact(for: user, withCompletionBlock: { (error) in
            print("Contact successfully created.")
            // then initialize chat with the just created user
            chatm?.start(with: user)
        })
    }
}
```

As soon as you create a user you should save the newly created one in Contacts with **ChatUser.createContact** method. In this manner his _first name_ and _last name_ become persistent in the cloud of Chat21 and will be available to everyone will connect to your chat.

## Sign in with existing user

To sign in with an existing user you can use **ChatAuth.auth** method:

```swift
let email = "MY EMAIL"; /** FIREBASE USER EMAIL **/
let password = "MY PASSWORD"; /** FIREBASE USER PASSWORD **/
ChatAuth.auth(withEmail: email, password: password) { (user, error) in
    if let err = error {
        print("Authentication error: ", err.localizedDescription);
    }
    else {
        if let user = user {
            user.firstname = "FIRST NAME";
            user.lastname = "LAST NAME";
            let chatm = ChatManager.getInstance()
            chatm?.start(with: user)
            chatm?.createContact(for: user, withCompletionBlock: { (error) in
                print("Contact successfully created.")
            })
        }
    }
}
```

Every time you sign in with a user remember to instantiate a Chat with that user with **ChatManager.start** method:

```swift
let chatm = ChatManager.getInstance()
chatm?.start(with: user)
```

## Sign in with Firebase

If you prefer you can sign in directly using Firebase users \(for example, if you already use Firebase for your App\). Simply remember to create a ChatUser with the Firebase UserId and then start the chat, as in the example below:

```swift
let email = "andrea@email.it"; /** FIREBASE USER EMAIL **/
let password = "123456"; /** FIREBASE USER PASSWORD **/
Auth.auth().signIn(withEmail: email, password: password) { (result, error) in
    if let error = error {
        print("Error while authenticating: \(error)")
    }
    else if let result = result {
        let firebase_user: User = result.user
        let user: ChatUser = ChatUser()
        user.userId = firebase_user.uid
        user.email = email
        user.firstname = "John";
        user.lastname = "Nash";
        let chatm = ChatManager.getInstance()
        chatm?.start(with: user)
        chatm?.createContact(for: user, withCompletionBlock: { (error) in
            print("Contact successfully created.")
        })
    }
}
```

## Custom authentication

If your app already has his own user base and you just want to add chat features to let users talk with each other the best way is to use **custom authentication**.

Custom authentication directly relies on Firebase. You must read Firebase documentation available here:

[https://firebase.google.com/docs/auth/admin/create-custom-tokens](https://firebase.google.com/docs/auth/admin/create-custom-tokens)

After you login with Firebase you'll create a ChatUser with the Firebase UserId as in the examples before, the start the chat with the **ChatManager.start** method  










