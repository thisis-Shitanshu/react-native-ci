# CONTINUOUS INTEGRATION IN REACT NATIVE
- In this project, we’ll take a look at how we can implement continuous integration in React Native. Specifically, we’re going to tackle the following topics:
    - Part 1: Introduction to continuous integration
    - Part 2: Starting CI with App Center
    - Part 3: Advanced CI with Bitrise

- What do I already know (Prerequisites)
    - [x] Basic knowledge of React and React Native.
    - [x] Redux
    - [x] Knowledge of testing tools such as Jest and Detox will be helpful but not required.
    - [x] Experience in using version control software such as Git.

- System specification:
    - [x] Machine should also be set up for React Native development.
    - [x] Android or Xcode needs to be installed.
    - [x] Yarn should be installed.

# The APPLICATION:
![](./ssHome.jpg)

# GETTING STARTED WITH CONTINUOUS INTEGRATION IN REACT NATIVE - PART 1: INTRODUCTION
- What is Continous Integration?
    - Continuous integration is the practice of frequently committing small changes to a shared repository.
        - This can range from once per day to multiple times per day.
    - Automated tests (unit, functional, integration) are then run to check if the whole system is still working correctly.
        - If it is, the changes can either be deployed to production or reviewed further.
        - If it doesn’t work, then the developer can easily fix the problem because the change is only minimal.
    - The main benefit of using continuous integration is to be able to detect errors early so that they can be fixed immediately.
    
## CI workflow in React-Native:
- One huge part of the CI workflow is the server which will ensure that the app is running correctly.
    - But unlike in a web project;
        - Where the CI server usually only installs dependencies then performs some tests.
        - In a mobile CI setup;
            - The server also has the responsibility to build the app.
                - That is, to come up with the .apk or .ipa file. 
                - This ensures that the app is building correctly each time new code is introduced.

- **The workflow:**
    *(This assumes that you already have an existing React Native project, and a code repository)*
    1. You connect your code repo to the CI server.
    1. You set up your CI server to build the app every time new code is committed to the *develop* branch of your repo.
    1. You create a new branch called *awesome-feature-1* off of your develop branch.
    1. You add the code for the new feature and add some tests.
    1. You test the changes locally by running the tests, building the app, and manually testing the new feature.
    1. Once you’re fairly sure that the app is working, you merge the *awesome-feature-1* branch to the *develop* branch and push it to your repo.
    1. The CI server “sees” that a new code has been added to the develop branch so it installs the dependencies and builds the app.
    1. After the app is successfully built, it runs the unit and functional tests.
    1. Once the tests pass, the *.apk* or *.ipa* files become available for download.
    1. If at some point, an error occurred while the CI server is doing all of those, an email is automatically sent to inform you that the build broke.
    1. You create a new branch off of the *awesome-feature-1* branch and call it *awesome-feature-1-fix-1*. 
        - From there, you work on the fix and merge it back to the *awesome-feature-1* branch and then to the *develop* branch once you’re done with the local tests.
    1. You push the *develop* branch to your repo and the cycle continues.

## CI Services for React-Native:
### Visual Studio App Center:
- App Center is a platform that allows you to;
    - continuously build,
    - test,
    - release,
    - and monitor apps.
- It supports GitHub, Bitbucket, and Visual Studio Team Services.
    - That means you can connect your code repo to any of these services.
        - So every time you push some changes to a specific branch, it will automatically build your app.
- App Center supports the following mobile app development platforms:
    - Swift
    - Objective-C
    - Java
    - Xamarin
    - React Native
- Doing development in any of these platforms, there is some form of support that allows you to implement continuous integration in an easier way.
    - For example, App Center supports automated UI testing with frameworks such as Espresso, Appium, and XCUITest.
        - Those frameworks are great if you’re developing a native Android or iOS app because you’re most likely using them already.
    - But if you’re a React Native developer;
        - You’re using something like Detox, you have no choice but to implement it by hand.
- The App Center is also a continuous delivery platform so it provides the following services as well:
    - Beta app distribution via Test Flight and Google Play Store.
    - Live code updates via CodePush.
    - App monitoring.
    - App analytics.
    - Push notifications.

### Bitrise:
- Bitrise is a continous integration and delivery platform.
- Its main focus is on mobile app development so the following platforms are supported:
    - Native iOS and Android
    - Cordova
    - Xamarin
    - React Native
- Bitrise is also beginner-friendly.
    - When it’s your first time using it, their built-in project scanner automatically detects the platform that you’re working with.
    - From there, it creates a base workflow that builds, tests, and deploys your app.
    - Once you’re ready to explore, you can use their workflow editor to customize each step of the build process.
        - Once you become confident in using the workflow editor, you can actually start editing the bitrise.yml directly in order to customize the build script.

## INTRODUCTION TO THE PROJECT
- Throughout the series, we will be working on a single project which we will use for testing the CI services.
    - The users can scroll through a list of Pokemon and mark their favorites.
    - Those favorites will be kept in local storage so the next time the user opens the app, they will still be marked as favorite.

# GETTING STARTED WITH CONTINUOUS INTEGRATION IN REACT NATIVE - PART 2: STARTING CI WITH APP CENTER
- In this section, you’ll learn how to use Visual Studio App Center as a CI server for the React Native app.
- Specifically, you’re going to learn the following:
    - How to setup a React Native project in App Center.
    - How to configure the project build.
    - How to run Jest tests.
    - How to use custom build scripts.

- These are the package versions that we will be using:
    - Node 8.3.0
    - React Native 0.50
    - Yarn 1.7.0
- Other dependencies:
    ```json
    "dependencies": {
		"prop-types": "^15.6.2",
		"react": "16.0.0",
		"react-native": "0.50",
		"react-native-vector-icons": "^5.0.0",
		"react-redux": "^5.0.7",
		"redux": "^4.0.0"
	},
	"devDependencies": {
		"babel-jest": "23.4.2",
		"babel-preset-react-native": "4.0.0",
		"jest": "23.5.0",
		"react-test-renderer": "16.0.0"
	}
    ```

## Initial Project Setup
1. Log in to your GitHub account;
    - create a new repo named ReactNativeCI.
    - This is where you will be pushing the project so that you can connect it to App Center.
1. Clone the project repo (the GitHub repo containing the starter project, not the one you just created) and switch to the starter branch:
    ```git
    git clone https://github.com/anchetaWern/ReactNativeCI.git
    cd ReactNativeCI
    git checkout starter
    ```
    - The starter branch contains the untouched version of the app that we will be working on.
1. Next, initialize a new React Native project using the same version I used.
    1. Copy the src folder and App.js file from the repo you cloned earlier and paste it into the project you just created.
    ```
    react-native init ReactNativeCI --version react-native
    cd ReactNativeCI
    ``` 
1. Next, install the dependencies and link the native modules:
    - Dependencies copied from the clone.
    ```
    yarn install 
    react-native link
    ```
1. Make sure that the app is still running.
    - This is important because if it doesn’t run locally then it’s likely that it wouldn’t build properly on the CI server either:
    ```
    react-native run-android
    react-native run-ios
    ```

## Setting up a Project on App center
- [x] Sign up for a new account on the App Center website. If you haven’t done so already.

1. Once your account is created, create a new app.
    - The number of apps you need to create depends on which platforms you need to support.
        - Example: ReactNativeCI-android
    ```
    Don’t worry if you don’t have a Mac. App Center doesn’t need you to be on Mac in order for it to build an iOS app. The only problem is actually running the app that it’s going to build. Apple is very strict when it comes to these things. You need the app to be signed with a provisioning profile and distribution certificate from a paid Apple Developer account if you want to run it on your iOS device.
    ```
1. Once the app is created, it will show you the getting started guide:
    1. Add the SDK to the project:
        - Enter the following line to add Crash and Analytics services to your app:
            ```
            yarn add appcenter appcenter-analytics appcenter-crashes --save-exact
            ```
    1. Link the SDK:
        - Link the plugins to the React Native app using the react-native link command. 
        ```
        react-native link
        ```
        - The SDK will ask for the app secret per platform.
        - The SDK will further ask whether or not to send crash reports and user events automatically, which we recommend you do for easy setup.
    - If you’re planning to build for both Android and iOS, you should also create the other app on App Center if you haven’t done so already.
1. Switch back to App Center and click on the Build menu on the sidebar.
    - It will ask you to connect your code repo.
    - Currently:
        1. I am adding a git repo to my recently created ReactNativeCI Project.
        1. I have then created a branch.
        1. I am pushing the changes to the repo.
    - Click on the gear icon to configure the build for this specific branch: *master*
    - From the Build configuration screen;
        - We’re setting the Build Variant to debug so we don’t have to supply a release keystore file.
        - The Build frequency is set to manual because we don’t really want to trigger a new build everytime we push some changes to the repo as we’re just testing things out.
        - All the toggle buttons should be set to Off for now.
    - Once you’re done with the configuration, click on Save & Build.
        - In my case, it took four minutes.
    - Once the build is complete;
        - It should present you with a button to download the build (a .zip file containing the .apk file).
        - But the app won’t really work because;
            - We haven’t signed it yet.
            - All apps need to be signed with a distribution certificate; 
                - even if it’s not going to be released on any app store.
        - *The only purpose of having you build is to make sure that the build succeeds and to get you acquainted with the App Center.*


## Shot out too: https://pusher.com/tutorials/