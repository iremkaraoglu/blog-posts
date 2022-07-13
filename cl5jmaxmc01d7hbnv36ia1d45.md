## UI Testing in React Native with Detox

Photo by Brett Jordan on Unsplash

Today, I would like to talk about UI tests, which is one of the things that I am responsible for years in the company that I work at. Let’s start with **what is UI testing.**

**UI testing** is basically what designers, testers and the frontend developers do maybe without even noticing it. When we are testing a screen, we check the components: The button is located in the right place, the content is correct, the button navigates the user to the X screen, and so on. We were all doing it and it was namely UI (User Interface) Testing. It can be done manually or [automated with a testing tool](https://www.perfecto.io/products/testcraft). Regardless of the method used, the goal is to ensure that all the UI elements meet the requested specifications.

The more features your application has, the harder it is to test manually. Automated tests help to ensure that your application performs correctly before you publish it while retaining your feature and bug-fix velocity.

In [Otsimo](https://otsimo.com/en/), we develop our products in React Native and one day we faced with a major bug that costs a lot for our company. Then, we realized that we should do something to prevent this situation to happen again. Also, we add features each day so we need to be sure that we are not breaking anything and continue developing with confidence. So, we ended up with the solution which is building End to End testing.

When it comes to the comparison of tests, Integration Testing has the highest maintenance cost, has the most dependencies, and slowest in execution speed but gives the highest confidence with respect to Unit and Component Testing. According to our reasons, it was fair enough for us to do Integration Testing, so we dived into UI Testing.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1657715154517/f6Bxp_tkdG.png)

Table for comparison of tests

To learn more about the test types you can check [React Native’s Testing documentation](https://reactnative.dev/docs/testing-overview).

#### Detox

[Detox](https://github.com/wix/Detox) is an end-to-end testing framework which is developed by [Wix](http://wix.com). This means it runs your app on an actual device/simulator and interacts with it just like a real user would. This type of testing can give a lot of confidence in your app and help automate a manual QA process. To learn more about how detox works, you may want to check [here](https://github.com/wix/Detox/blob/master/docs/Introduction.HowDetoxWorks.md).

Detox was the best fit for us because it is built from the ground up to support React Native projects as well as pure native ones. It is built for gray box end-to-end testing for iOS and then started to support Android as well. It offers more capabilities for iOS rather than Android and since we are not fond of Android default emulator, our tests only run on iOS.

#### Testathon

In our company, we spared two days just for testing in the developers team. We called it **Testathon.** We wanted to have UI Tests and have the artifacts (logs and video recordings from the tests) to be stored. Therefore, we created a new backend service to store artifacts that runs on Raspberry PI in the office near our real UI testing device.

We separated into two groups, one group listed down the test scenarios and wrote tests, the other one worked on backend service to store the build artifacts to our Raspberry PI in the office. I am not going to dive into this part because this is not relevant to the topic I would like to cover but as a brief, I may say, we also added these tests to our CI/CD process. When one of the tests fails, It doesn’t pass the CI and the fail message comes with our internal Slack bot. Gitlab does not have any feature like having these logs and videos to show, we needed to build this. We store each one for 90 days. These all are built using [Go](https://golang.org). Let’s go back to the first group’s part which is about the test itself.

#### Creating UI Test Scenarios

**It’s better to focus on what is essential for your application**. For example, if registering is mandatory in your application, you should better be checking if sign up and login screens and their functions work as intended!

While doing that, you have to code the tests as if you are testing manually. What do I mean by that? When you are manually testing, you check the screen like if the title and the content are correct. Then, check the the button if it is located correctly and you press a button. Next, you wait for an action like navigating to a new screen. You understand you have navigated correctly because you saw a new title and the content are changed. Same way, you should code your UI test. You should write the commands like what you normally do when you are testing it manually.

For example:   
1\. Check if there are “Sign Up” and “Sign In” buttons on the screen.   
2\. Press the “Sign In” button and wait for a screen to have e-mail input field.   
3\. Type correct email address and password and expect to see the Home screen  
…

As a company, we need to be sure that our users or potential users should successfully sign up/sign in, they should be able to purchase a premium membership and successfully open the screens in the application without any crash. We started with these essential tests. Before diving into the code, let’s see my frequently used stuff which is taken from the API documentation, and see an example to make it clear.

#### The [Device](https://github.com/wix/Detox/blob/master/docs/APIRef.DeviceObjectAPI.md) Object

`device` is globally available in every test file, it enables control over the current attached device (currently only simulators are supported).

*   `device.installApp()  
    `By default, `installApp()` with no params will install the app file defined in the current `configuration`.
*   `device.uninstallApp()  
    `By default, `uninstallApp()` with no params will uninstall the app defined in the current `configuration`.
*   `device.launchApp()  
    `Launch the app defined in the current `configuration`.
*   `device.terminateApp()  
    `By default, `terminateApp()` with no params will terminate the app file defined in the current `configuration`.
*   `device.openURL(url)  
    `Mock opening the app from URL.
*   `device.enableSynchronization()  
    `Enable (restore) synchronization (idle/busy monitoring) with the app — namely, resume waiting for the app to go idle before moving forward in the test execution. Enabled by default.
*   `device.disableSynchronization()  
    `Temporarily disable synchronization (idle/busy monitoring) with the app - namely, stop waiting for the app to go idle before moving forward in the test execution (rather, resort to applying unrecommended `sleep()`'s in your test code...).
*   `device.setURLBlacklist([urls])` Exclude syncrhonization with respect to network activity (i.e. don’t wait for network to go idle before moving forward in the test execution) according to specific endpoints, denoted as URL reg-exp’s.

#### [MATCHERS](https://github.com/wix/Detox/blob/master/docs/APIRef.Matchers.md)

Detox uses [matchers](https://github.com/wix/Detox/blob/master/docs/APIRef.Matchers.md) to match UI elements in your app. The ones that I use so often are below.

*   `by.id()`   
    Match elements with the specified accessibility identifier. In React Native, this corresponds to the value in the `[testID](https://reactnative.dev/docs/view.html#testid)` prop.  
     — `testID  
    `Used to locate a view in end-to-end tests.
*   `by.label()  
    `Match elements with the specified accessibility label (iOS) or content description (Android). In React Native, this corresponds to the value in the `[accessibilityLabel](https://facebook.github.io/react-native/docs/view.html#accessibilitylabel)` prop.
*   `by.text()  
    `Match elements with the specified text.

#### [ACTIONS](https://github.com/wix/Detox/blob/master/docs/APIRef.ActionsOnElement.md)

Detox uses [actions](https://github.com/wix/Detox/blob/master/docs/APIRef.ActionsOnElement.md) to simulate user interaction with those elements. My frequently used ones are below.

*   `.tap()` Simulates a tap on the element at the specified point, or at element’s activation point if no point is specified.
*   `.typeText()  
    `Simulates typing of the specified text into the element, using the system’s builtin keyboard and typing behavior.
*   `.clearText()  
    `Simulates clearing the text of the element, using the system’s builtin keyboard and typing behavior.
*   `.tapReturnKey()  
    `Simulates tapping on the return key into the element, using the system’s builtin keyboard and typing behavior.

[**wix/Detox**  
*Detox uses matchers to match UI elements in your app. Use actions to simulate use interaction with elements and…*github.com](https://github.com/wix/Detox/blob/master/docs/APIRef.Matchers.md "https://github.com/wix/Detox/blob/master/docs/APIRef.Matchers.md")[](https://github.com/wix/Detox/blob/master/docs/APIRef.Matchers.md)

#### [EXPECTATIONS](https://github.com/wix/Detox/blob/master/docs/APIRef.Expect.md)

Detox uses expectations to verify those elements are in the expected state.

*   `.toBeVisible()  
    `Expects the element to be visible on screen.
*   `.toExist()  
    `Expects the element to exist within the app’s current UI hierarchy.

Bear in mind:  
If we are checking the element’s visibility, we are expecting that the element is on the screen (not scrolled or something) and has at least **75% opacity**. Alternatively, you can expect the element `.toExist()`, but that might result with an element that a user can't interact with. `.toExist()`checks for the current UI hierarchy.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1657715155930/Fcobwtc8T.jpeg)

Photo by [Cookie the Pom](https://unsplash.com/@cookiethepom?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

#### 👩🏻‍💻 EXAMPLE

> **Talk is cheap, show me the code.**

Here is the coding part 🐝

I created a [**repository**](https://github.com/iremkaraoglu/ReactNative-Detox-Example) to show you an example. Now, I will summarize it here with simpler code.

I created two screens and they have the following UI elements inside the render methods. I added **testID** attribute to the elements to reach during tests.

Then, I added the **detox** library and made the configurations.

Now, time to write the first test !

*   First, we should see the sign up and sign in buttons on the screen. Expect the elements’ visibility by their id (which are the testID’s we recently gave)
*   Secondly, sign in button should be clicked and it should navigate to a new screen. Its ID is “emailField” and it should be visible.
*   Thirdly, emailField and passwordField should be filled by the values “irem@test.com” and “123456” respectively. Then the “My Courses” text should be visible on the screen. (This time, I checked by text not by id — this is a separate screen and it just has a text component, so I didn’t place it here)

#### DEMO 🎉🎉🎉

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1657715160433/JGKv6RpVL.gif)

#### Hint — 1💡

It is more practical to check the elements by ID because the machine finds the elements by id more easily and also it avoids the ambiguity. For example, there might be “Done” button twice in a screen, one on the page and one may occur in a popup. If we expect the element by text as “Done”, it won’t be able to decide about which one we are aiming for.

#### Hint — 2💡

If there are multiple elements on the screen (most probably this will be the case) if one of them’s visibility gives you the confidence that you are on the right screen, you do not have to check for every element. However, if there are essential ones, it’s better to check them too.

While you are running your UI test, make sure that you opened Simulator by XCode to see how the tests run on a simulator. Therefore you may easily see where it got stuck; in a popup or a screen that you might have forgotten for instance. Other than that, it gives me joy to see someone is clicking on the buttons and testing the app :)

#### Hint — 3: Common Mistake 💡

You may see console warn messages appear during the UI tests just like in the gif above. When there is a warn message and it comes across to the element you expect it to be visible or will have an action with it, it ends up with failure because warn messages block the button’s visibility and the actions. You may avoid having console warn messages and learn here how to achieve that:

[**How do you hide the warnings in React Native iOS simulator?**  
*I found that even when I disabled specific warnings (yellow-box messages) using the above mentioned methods, the…*stackoverflow.com](https://stackoverflow.com/questions/35309385/how-do-you-hide-the-warnings-in-react-native-ios-simulator "https://stackoverflow.com/questions/35309385/how-do-you-hide-the-warnings-in-react-native-ios-simulator")[](https://stackoverflow.com/questions/35309385/how-do-you-hide-the-warnings-in-react-native-ios-simulator)

#### Useful Video Resources

I personally love reading the Detox documentation because it gives me all the information I need. However, if you would like to search for more resources here is a list of my favorite others.

*   [Real World e2e Testing with Detox by Vojtech Novak](https://youtu.be/_neMz2_6u20) :   
    It is a presentation in React Native EU 2019 Conference. As a visual learner, I like to see someone explains a technology with a presentation. This video was my first resource before diving into the Detox! After getting to know the concepts, using the documentation became easier!
*   [Detox: Tackling the flakiness of mobile automation by Viktorija Sujetatie](https://youtu.be/4rU0IGEt6OQ)  
    It is again a presentation and it’s by Mobile QA Engineer of Wix. She revises React Native, mentions why they need to implement their own framework for E2E testing and explains Detox.

Please like this article if you liked the content, it helps me to get motivated and create more 🌟