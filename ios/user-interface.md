# User Interface

## Introduction

Chat21 provides many ready-to-use UI components. Beyond these components Chat21 has some extension points that allows a developer to plug his own custom components for improving or customizing chat's default behaviour.

All of these examples are available in the **Chat21 Swift Playground** project 👉🏻 [https://github.com/chat21/chat21-swift-playground](https://github.com/chat21/chat21-swift-playground). We strongly recommend you to install and configure the playground and use it to try all these examples.

If you have feedbacks or suggestions about this documentation, please send us an email at **support@frontiere21.it**. We are always open to improvements coming from Chat21 Community!

## Open Conversations View

The first view that a user expects in a chat is the list of his recent conversations. Chat21 provides this feature with the **ChatUIManager.openConversations** method:

```text
ChatUIManager.getInstance()?
    .openConversationsView(
        asModal: self,
        withCompletionBlock: nil)
```

## Open a Messaging view with someone

If you want to place a button around your app to allow the _signed_ user to select a contact from available contacts and open a conversation with him you can use **ChatUIManager.openSelectContactView** method. This method opens a modal dialog that allows the selection of a user among those already registered in the App:

```text
ChatUIManager.getInstance()?.openSelectContactView(asModal: self, withCompletionBlock: { (contact, canceled) in
    if (canceled) {
        print("canceled")
    }
    else {
        ChatUIManager.getInstance()?
            .openConversationMessagesViewAsModal(
                with: contact,
                viewController: self, 
                withCompletionBlock: { () in
                    print("Messages view dismissed.");
                });
    }
})
```

As you can see, as soon as a contact is selected, the completion block is called with the contact parameter populated \(type ChatUser\). This contact is then passed as a parameter to **ChatUIManager.openConversationMessagesViewAsModal**. This last method opens a dialog to instant messaging with the target user.

The decoupling of the Contact Selection view from the Contact Messaging view allows for an easy plug of alternative Contact Selection components, allowing a developer to choose contacts from alternative contacts DBs \(different from Chat21 native contact management\).

## Messaging without Contact Selection view

Design requirements often opt to place a button inside the Profile View of a user to directly message with him. If you want to bypass contact selection view and message to a user directly, i.e. tapping a button on UI, just like you message to a user from his Instagram Profile, you need to create a ChatUser instance with the following **minimum** properties set:

* ChatUser.userId
* ChatUser.firstname
* ChatUser.lastname

Please notice that while _userId_ must correspond to a valid Firebase user, _firstname_ and _lastname_ are totally arbitrary. You must provide this two properties from your user database or from Chat21 Contact Management.

In this example a contact is created with the minimal information to start a conversation:

```text
let contact: ChatUser = ChatUser()
contact.userId = "5aaa99024c3b110014b478f0"; // valid Firebase uid
contact.firstname = "Andrew";
contact.lastname = "Leo";
ChatUIManager.getInstance()?.openConversationMessagesViewAsModal(with: contact, viewController: self, withCompletionBlock: { () in
    print("Messages view dismissed.");
});
```

{% hint style="info" %}
Every contact you want to open a conversation with must be an already registered Firebase user \(with a valid Firebase userId\).
{% endhint %}

## Embed View components

Sometimes you just need the raw UI Component just to embed the Component itself into another View \(i.e. a tab in Tabbed Application\). To access the raw components you can use the **getCOMPONENT-NAMEComponent\(\)** methods of the **ChatUIManager** class. Here follows some code snippet to get the main components of the Chat21 framework

```text
let vc: UINavigationController? = 
            ChatUIManager.getInstance()?
            .getConversationsViewController()
```

```text
let vc: UINavigationController? = 
            ChatUIManager.getInstance()?
            .getSelectContactViewController()
```

```text
let vc: UINavigationController? = 
            ChatUIManager.getInstance()?
            .getMessagesViewController()
```

```text
let vc: UINavigationController? = 
            ChatUIManager.getInstance()?
            .getSelectGroupViewController()
```

## Pluggable User Interface components

### Plug User Profile View

Chat21 APIs does not provide a "Profile view" because it is generally provided by the Host application, showing User info relative to the same App.

Chat21 provides instead an easy way to plug in the profile view of your Application in all the UI points where this view is invoked. Simply use the **ChatUIManager.pushProfileCallback** callback to plug your external view. Here follows an example:

```text
ChatUIManager.getInstance()?.pushProfileCallback = { (user, vc) in
    let storyboard: UIStoryboard  = UIStoryboard.init(name: "Main", bundle: nil);
    let profileVC: ProfileViewController = storyboard.instantiateViewController(withIdentifier: "user-profile-vc") as! ProfileViewController    
    profileVC.user = user // Pass the chatUser to the view to get info on the user
    vc?.navigationController?.pushViewController(profileVC, animated: true)
}
```

In the previous example a "profile view" is created using a View Controller in _Main.storyboard_. The **ProfileViewController.user** parameter is used to get info about the current user showing his informations.

### Plug your own Select Contact View

While Chat21 has its own Contact management component sometimes you probably want to have your own source of contacts. Chat21 provides you the option to plug your own Contact Selection View.

This view is activated every time a user taps on the "write to" button, on the top right of conversations summary \(generally the main view\).

![Native &quot;Write to&quot; button](../.gitbook/assets/image%20%282%29.png)

Your custom view must simply conform to [**ChatSelectContactProtocol**](https://github.com/chat21/ios-sdk/blob/master/Chat21/Chat21UI/view/ChatSelectContactProtocol.h). You can find an instance of a custom contact selector 👉🏻 [MySelectContactViewController](https://github.com/chat21/chat21-swift-playground/blob/master/MyChat/MySelectContactViewController.swift) in the [Swift Playground](https://github.com/chat21/chat21-swift-playground) project.

Once you write in your own class you can simply plug it in your project with the following lines:

```text
let storyboard: UIStoryboard  = UIStoryboard.init(name: "Main", bundle: nil);
let select_user_vc: MySelectContactViewController = storyboard.instantiateViewController(withIdentifier: "select-user-vc") as! MySelectContactViewController
ChatUIManager.getInstance()?.selectUserViewController = select_user_vc;
```

As you can see from sources this view simply reply with a callback containing the selected contact \(type: ChatUser\) as parameter.











