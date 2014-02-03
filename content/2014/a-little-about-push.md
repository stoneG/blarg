Title: A little about push
Date: 2014-02-02 23:30
Author: Sitong Peng
Tags: iOS, APNS
Slug: a-little-about-push
Summary: A little about iOS push notifications.

Last year I had the chance to take some time and get a better understanding of how push notifications work for iOS applications. But before I talk about that, it helps to understand why push notifications exist in the first place.

Apple developed iOS as a closed platform operating system. The source code is mostly closed except for the publicly available portions that developers use in networking programming or to interface with the camera or all the other fancy things that apps do. One of the most important design decisions made at Apple was to heavily restrict the capabilities of daemonized apps (apps running in the background). In fact, Apple's guidelines explicitly state that only apps such as music players, VoIP clients, and GPS navigation systems are allowed to run background tasks. That essentially means that once a user leaves your app, you're dead in the water until the user decides to tap your lovely retina icon again... at least that would have been the case if it weren't for push notifications.

Push notifications exist for the express purpose of letting applications pull users back into their experience. They're basically shouting "Hey, look at me, I have something to show you!"

The life of a push notification starts on an application. The application will ask iOS for a unique device token that identifies the device, sort of like an IP address. As you'd imagine, it's pretty important to know the address of the device you want to send something to. The operating system doesn't have this information regularly, it actually makes a request to the Apple Push Notification Service (APNS), which divulges a unique token for that specific iOS build on that specific device. The application should then pass the token to its backend server (this is the thing that I've been working on at [EAT Club](https://www.myeatclub.com)).

Once you want to send a push notification to a specific user (for instance, a "Your food is ready for pickup!" notification that we send to users who have ordered a meal that day), just pluck that device token out of your database table and pass it to your push server to fire off to APNS. There are also third parties that exist as the middle man push server between your backend servers and APNS (see UrbanAirship). While it would be awesome if we could build servers that directly sent out pushes to iOS devices, Apple unfortunately maintains ultimate gatekeeper status and all your push notification requests must be routed through APNS. Such is the plight of developing on a closed source platform.

Then if all is fine and dandy, APNS will send your push to the user's device - along with a custom sound (i.e. the Gmail app), custom alert text, and even let you specify where the user lands when they open the notification (i.e. opening up the Twitter app and immediately dropping you on the tweet that just mentioned your handle).
