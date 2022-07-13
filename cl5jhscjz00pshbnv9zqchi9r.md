## Accessibility Support in React Native Applications


Here in [Otsimo](https://www.otsimo.com/en), we are developing applications for the kids who have learning disorders or with autism spectrum disorder. We care about the equality in education and support the special children’s needs. Our apps are designed to be used by parents and their lovely children. What about our visually impaired users? How can they easily use our app? Can blind people use mobile applications? What about writing code? Can they write a code even they cannot see? Let’s talk about it first.

![1*Ovh3J1LDY92pRxifiokEWw.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1657707172417/DKoyAdV1u.png align="left")

**Tuukka Ojala** is a blind developer who listens the code line by line by screen reader. He speaks in detail about how he uses his computer and writes code in [his blog post](https://www.vincit.fi/fi/software-development-450-words-per-minute/). He is one of the best examples that visually impaired people do exist, they have jobs just like us and use technological devices as well. They are able to use these devices, thanks to the accessibility features of softwares.

What can we do for visually impaired people not only as humans but also as developers? We can make the apps suitable for their needs. We can add accessibility features to help their usage.


![1*RvSQk2fTQ3UI6qhlbe00zg.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1657707209579/9VQRZkQin.png align="left")

We generally think like only people who have blindness use screen readers, but it is only 64% of users of screen readers. People who have low vision are 39% and there are also cognitive, deafness, motor and other ilnesses who use screen readers.

To support accessibility in a mobile application project, emphaty is the key to understand. I am going to mention **how to make a mobile application more accessible for people who use screen readers** on a React Native project especially for iOS devices.

My development started as testing it on iOS Settings. It reads the text of a button and then says “button” to let the user know it’s pressable. If you have already implemented at least a page of your mobile application, you can check out what are the necessities. Probably VoiceOver won’t understand that it is a button and won’t say “button” after reading the text of your button. So you need to make it accessible first (by default, all touchable elements are accessible) and declare accessibility role as button. If these don’t make sense, don’t worry. Keep reading, everything will be clear.

## Let’s start

To ensure that VoiceOver can access your component, type 
**accessible={true}** in your component.

Whenever you have a control, add all of the accessibility information you can. At a minimum, implement the following properties
- accessibilityRole (button, text, link, etc.)
- accessibilityLabel (when the operating system can’t derive a useful read out on what this control does)
- accessibilityHint (when the label alone won’t tell the user what’s going to happen when they click it)

### AccessibilityRole

To inform the user that it’s a button, add 
**accessibilityRole=“button”** to your component in the same way.

Some accessibility roles you may probably use often are listed below. There are bunch of other roles so you can check it out for more to find out which suits your needs.

**alert**: Used when an element contains important text to be presented to the user.

**button**: Used when the element should be treated as a button.

**header**: Used when an element acts as a header for a content section (e.g. the title of a navigation bar).

**image**: Used when the element should be treated as an image. For example, it can be combined with button or link.

**switch**: Used to represent a switch which can be turned on and off.

**text**: Used when the element should be treated as static text that cannot be changed.

### AccessibilityState

You can inform the user by adding **accessibilityState={{ disabled: true }}** if the button is disabled so the user can’t press that one. VoiceOver reads it as “dimmed” when it’s disabled. You can also use **accessibilityState={{selected: true}}** if you want it to be read as “selected”.

Let’s see some code to make it clear. Following code renders a Pressable component with a “This is an example” text on it and Accessibility VoiceOver reads it as “This is an example dimmed button” on iOS.

%[https://gist.github.com/iremkaraoglu/c57364e09498a228dcc17cb79934e070]

AccessibilityState takes an object which is not limited with the disabled key. You may also add selected, checked and so on.

**Selected** is mostly used in the components that include buttons and users are able to select only one. Therefore, VoiceOver informs the user about which is “selected”.


![1*EqpErTA1BCDSI3z8suPIOw.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1657707920060/rXuhXWUO1.png align="left")


In the above example, “While iPhone is Locked” is selected so these two rows are read as “Always” and then “Selected While iPhone is Locked”.

When it comes to “checked”, you may use it on your switch buttons just like iOS’ airplane mode on — off. Also, make sure that you are writing **accessibilityRole=“switch”**


![1*yyC4yeFbi_hLTFPy2ll7lg.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1657708005784/lNjb2I3vI.png align="left")

Above example is read as “Do not disturb **on** double tap to toggle setting, scheduled **off** double tap to toggle setting” by VoiceOver.

![1*e3255Xu2xS0Gq6L26hnrqg.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1657708028898/gUewPr_e8.png align="left")

### AccessibilityHint

An accessibility hint helps users to understand what will happen when they perform an action on the accessibility element when the result from the accessibility label is not clear. Such as telling the user that back button’s action as “Navigates to the previous screen”.

Bear in mind that if the user has hints enabled in the device’s VoiceOver settings, VoiceOver reads the hint after reading the label. When it comes to Android, TalkBack (Android’s screen reader) will read the hint after the label as well but hints cannot be turned off on Android.

## Did you know that VoiceOver uses Machine Learning ?

![1*wBolfiakc2ClJM_gn6RpOA.jpeg](https://cdn.hashnode.com/res/hashnode/image/upload/v1657708051906/ON9eOj_ApV.jpeg align="left")

You may think like it just reads and we give the roles, states, labels to make it read. Where is Machine Learning in this?

As a personal experience, while using our application, I realized it reads my iconic button (which has a + sign on it) as “Add” but we didn’t provide any accessibility information to it. I thought probably it just reads the icon’s name but icons are just unicode numbers and voice over doesn’t know it’s name.

While investigating this with our CTO, we tested it by opening the gallery and it started to read the photographs like “image, a baby crawling on the floor”. Then we found **“VoiceOver Recognition”** feature which uses on-device machine learning models. According to Apple, It improves accessibility of an app that have no accessibility information such as identifying the state of buttons or toggles.

As a user, you can switch recognition features **ON** under the **VoiceOver Recognition** title. If **Image Descriptions** is on, VoiceOver speaks descriptions of images in apps and on the web. If **Text Recognition** is on, VoiceOver speaks descriptions of text found in images. When it comes to **Screen Recognition**, it automatically make apps more accessible by recognizing items on the screen and especially this one is what makes your mobile application more accessible. For instance, it identifies the state of buttons or toggles, and by grouping related items together.

So, especially for icons and images, if you don’t want VoiceOver to read your visual component according to Machine Learning result, just give it an **AccessibilityLabel** to specify what you would like to be read. Otherwise, if your screen recognition turned on iOS users may experience some readings not matching what you have intended.

## Recommendation

I recommend two movies ([Theory of Everything](https://www.imdb.com/title/tt2980516/) and [Gleason](https://www.imdb.com/title/tt4632316/?ref_=fn_al_tt_1)) to help you understand how accessibility features are beneficial for people. As you may know, Stephen Hawking is one of the well known people about experiencing Voice Technologies. Helping them and making their lives easier is our responsibility.

## Conclusion

This article was only for supporting screen readers in mobile applications. As Otsimo development team, our work about accessibility is still going on. Now, we are searching for taking input just by head or eye movements and how we can add these features to our products.

I hope this article was beneficial for you and will help you to support accessibility in your products. If you are not developing anything at least having awareness is important too.

Thank you for reading, if you like this article and want to hear more please like this article. If you want to be notified about my next article, you can subscribe to my newsletter.

This article is originally posted [on my medium blog](https://www.iremkaraoglu.medium.com). See you in the next article! 🦋

