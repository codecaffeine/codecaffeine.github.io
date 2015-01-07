---
layout: post
title: "Cheap & Easy Buttons for iOS Developers"
date: 2011-12-08 18:06
comments: true
categories: ["iOS", "UIButton"]
---
The UIButton controls in iOS are very basic. Just a white rounded rectangle with a gray border and a label. They're made to look similar to a single grouped UITableView cell. Because of this, it's easy to end up with a boring iOS app without creating your own images for button. So what's a developer to do?

<!--more-->

My first bit of advice would be to work with a graphic designer. A graphic designer can help you create a style for you app and quickly create all the resources (including button images) you may need. Unfortunately, graphic designer don't grow on trees and the good ones would need to be paid. For a professional quality app, you should really work with a graphic designer.

Sometimes though, you're just looking for a quick pleasant looking button. Here are some great resources I use for creating button images.

__Apple's [Dashcode](http://developer.apple.com/library/iOS/#documentation/AppleApplications/Conceptual/Dashcode_UserGuide/Contents/Resources/en.lproj/Introduction/Introduction.html)__ has a pretty powerful button maker. There are a bunch of preset button templates that you can choose from but Dashcode's real power is the button inspector which has a lot of styles and effects.

![Dashcode's Button Inspector](/images/ButtonStyle.png)

__Cédric Luthi's [UIKit Artwork Extractor](https://github.com/0xced/UIKit-Artwork-Extractor)__ is an iOS Simulator app that can be used to download images from UIKit and various other iOS frameworks. You can search for images used the the framework like buttons and save these to your hard drive.

![UIKit Artwork Inspector search](/images/UIKitArtworkSearch.png)

The UIArtwork Kit Extractor also has a tab that uses Apple's private UIGlassButton class to create a real awesome looking glass button. Just don't use the [UIGlassButton code](http://twitter.com/#!/schwa/status/9288691077) in your shipping app. Otherwise you will probably be rejected from the App Store.

![UIKit Artwork Inspector glass button creator](/images/UIKitArtworkGlassButton.png)

__[House of Buttons](http://houseofbuttons.tumblr.com/)__ and __[Dribbble](http://dribbble.com/search?q=button)__ are great resources for getting button inspirations. Just don't steal their work. More importantly, [Dribbble](http://dribbble.com) is a great resource for meeting graphic designers so you can make quality apps.

Finally, if you are an __[Acorn](http://flyingmeat.com/acorn/)__ user, they have a pretty [awesome tutorial](http://flyingmeat.com/acorn/docs/tutorials.html) including one on [making buttons](http://flyingmeat.com/acorn/docs/making%20a%20custom%20web%20button.html) using vectors so you can resave different sizes. This is handy for dealing with retina display images.
