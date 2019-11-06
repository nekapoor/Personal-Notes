

### How do I run a react-native app on an Android device?

The first steps in making this work is that you need to turn on developer settings on the android phone. Google around to see how you can turn on developer settings before moving on.

Next, install the ADB command line tool. Using homebrew, you can run it with

``` 
brew cask install android-platform-tools
```

To see a list of devices that are available, type

```
adb devices
```

In order to see the android device listed, the phone must be connected to the computer.

Note: when I first ran `adb devices` I saw that the phone was unauthorized. In order to fix that, I made sure the phone was unlocked and then I ran these commands:

```
adb kill-server
adb start-server
```

After running these commands, on the phone screen, I saw a prompt to authorize the phone. 

I ran `adb devices` again and the phone looked authorized. 

From the project folder, I ran

``` 
react-native run-android
```

 and the app started up on the phone. 



### How do I start a react-native app from scratch and get started with React Navigation and a workable folder structure?

I had been looking for boilerplate react-native apps but nothing felt right everything came with drawbacks. So, instead I went looking for how to start a react-native app from scratch (and without using create-react-native-app). 

I found this [tutorial](https://medium.com/@filipvitas/setup-react-native-app-from-scratch-7f42cbeb4b01) and decided to use it.

Here is an overview of the steps:

1. Install the react native CLI globally

   ``` 
   npm install -g react-native-cli
   ```

2. Initialize your project

   ```
   react-native init myProject
   ```

3. cd into the project and run the app for iOS and Android

   ```
   react-native run-ios
   react-native run-android
   ```

   

With this base app, there are a few other things I want to do: 

1. Use React-Navigation for routes and navigation
2. Setup a basic folder structure to get the app up and running

Let's go through these in turn

#### React Navigation

First, install react-navigation and a couple of other related dependencies

```
yarn add react-navigation
yarn add react-native-reanimated react-native-gesture-handler react-native-screens@^1.0.0-alpha.23
yarn add react-navigation-stack
```

Then in your app, create a file called `AppNavigator.js` (use whatever name you want). Here is an example of that file for me:

Next, I create a file called `AppNavigator.js` (you can name it whatever you want). Here is what that file looks like:

```javascript
import { createStackNavigator } from 'react-navigation-stack';
import { createAppContainer } from 'react-navigation';

import ImageCapture from './ImageCapture';
import ImageDisplay from './ImageDisplay';

const AppNavigator = createStackNavigator(
  { 
    ImageCapture: ImageCapture,
    ImageDisplay: ImageDisplay
  },
  { 
    initialRouteName: 'ImageCapture' 
  }
);

export default createAppContainer(AppNavigator);
```

The `ImageCapture` and `ImageDisplay` imports were for the app that I specifically was working on. Inside the `createStackNavigator` method, be sure to include a route to all of the routes you want to include in your app. We'll learn below how to actually route to these screens. 

Then, in my App.js file, I did this:

```react
import React, { Component } from 'react';

import AppNavigator from './AppNavigator';

class App extends Component {
  render() {
    return (
      <AppNavigator />
    )
  }
};

export default App;
```

By rendering the AppNavigator, it will take the `initialRouteName` and render that screen. 

Let's say you're on the `ImageCapture` and you want to route to the `ImageDisplay` screen. How do you do this? In the `ImageCapture` screen, wherever you want to redirect, you would write

```javascript
this.props.navigation.navigate('ImageDisplay')
```

If we want to pass props to `ImageDisplay`, we would do it like this:

```react
this.props.navigation.navigate('ImageDisplay', { 
	imagePath: url, 
	originalImage: data.uri,
	capturedOverlayWidth: capturedOverlayWidth,
	capturedOverlayHeight: capturedOverlayHeight,
})

```

I'm not sure if this is the correct way to do this, but in the `ImageDisplay` component, if we now want to reference those props, they all can be accessed through this:

```javascript
this.props.navigation.state.params
```

This will include a hash with all of the data. So if we want the `originalImage`, for example, we would write:

```javascript
this.props.navigation.state.params.originalImage
```



#### Folder Structure

The root folder of your project will have an `iOS` folder, an `Android` folder, a `node_modules` folder and maybe some others. When you first start your react-native app, there will be an `App.js` and an `index.js` file, among other files. 

Let's first create a `src` folder. And within that folder, create two more folders: `modules` and `screens`. In the `screens` folder is where you'll put your high level screen files. In the modules folder is where I put my `AppNavigator.js` file. 

Here is what the `App.js` file now looks like:

```javascript
import React, { Component } from 'react';

import AppNavigator from './src/modules/AppNavigator';

class App extends Component {
  render() {
    return (
      <AppNavigator />
    )
  }
};

export default App;

```

Here is what the `AppNavigator.js` file  now looks like:

```javascript
import { createStackNavigator } from 'react-navigation-stack';
import { createAppContainer } from 'react-navigation';

import ImageCapture from '../screens/ImageCapture';
import ImageDisplay from '../screens/ImageDisplay';

const AppNavigator = createStackNavigator(
  { 
    ImageCapture: ImageCapture,
    ImageDisplay: ImageDisplay
  },
  { 
    initialRouteName: 'ImageCapture' 
  }
);

export default createAppContainer(AppNavigator);
```

