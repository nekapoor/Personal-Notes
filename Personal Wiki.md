

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



### How do I fix this error: `error Incorrect integrity when fetching from the cache`?

I was trying to install an npm package and this error came up. Here is how I fixed it.

Run this command

```
yarn cache clean
```

Then try to re-install the package.

### After taking a picture using the camera on android, the captured file gets stored in cache. How do I persist this image into disk on the camera?

I tried using the packages `react-native-fs` and `rn-fetch-blob`. But the only one I could get working was the `rn-fetch-blob` package. 

First, install it:

````react
import RNFetchBlob from 'rn-fetch-blob'
````

Note that depending your version of react-native, you may need to link this package. You can do that by running: 

```react
react-native link rn-fetch-blob
```

As a reference, here is an [API link](https://github.com/joltup/rn-fetch-blob/wiki/File-System-Access-API) with methods `rn-fetch-blob` supports.

The general idea is that after an image was captured, I attempted to create a folder in the Android file system. The first time the app was run, this would succeed. But after that, an error would occur saying the folder already existed. I just caught that error and didn't do anything. Here is the code for this:

```react
const dirs = RNFetchBlob.fs.dirs
console.log(dirs.DocumentDir)

const absolutePath = `${dirs.DocumentDir}/TestImages`

RNFetchBlob.fs.mkdir(absolutePath)
.then(() => { 
console.log(" Dir Created ")
})
.catch((err) => { 
console.log('Error creating dir; could be that it already exists')
})

```

Above, I created a folder called `TestImages`. I used `dirs.DocumentDir` to store the images in that particular folder. Click [here](https://github.com/wkh237/react-native-fetch-blob/wiki/File-System-Access-API#dirs) to see a list of other folders that (I think) you can write to. 

```react
RNFetchBlob.fs.cp(captured_image_path, absolutePath + '/test.jpg')
.then(() => { 
  console.log('yes') 
})
.catch((err) => { 
  console.log(err) 
})
```
This next part is where we actually copy over the file from where it lives in the cache to the path where we want it. 

One note about the `captured_image_path` value. It will look something like this:

```
file:///data/user/0/com.find/cache/ReactNative_cropped_image_1827472054665065924.jpg
```

You'll need to strip away the `file://`, leaving you with the `captured_image_path` holding the value

```
/data/user/0/com.find/cache/ReactNative_cropped_image_1827472054665065924.jpg
```

in order for this to work. 

This should be everything you need to make this work. 



### I wanted to sync a picture and associated json data with firebase (the picture with cloud storage and json with the firestorm database). But the idea came up that we could store the image as a base64 string. How do we do this in react-native?

Let's assume we've taken a picture and we have the uri path for the image. The uri path is the image that looks like 

```
/<path>/<to>/<image>
```

I used react-native-fetch-blog and this code will generate a base64 string from the image

```react
RNFetchBlob.fs.readFile(imagePath, 'base64')
.then((base64Data) => {
	console.log(base64Data)
})

```

Note that `imagePath` is the path to the path listed above. 

Now,  the `base64Data` is a string and we can use that data to send it to firebase however we feel. 

### What are some of the questions that come to mind when training images for a convolutional neural network?

1. What if the images in question are a different size? Can a CNN account for this? 
2. Can we use an entire image if only a small portion in the middle is the relevant part? Or do we need to crop? How will the CNN look at the other areas that aren't relevant to the processing? 
3. 

### When you receive a "react native mismatch version" error, how can you fix it?

I used [this](https://stackoverflow.com/questions/47763824/react-native-version-mismatch) stack overflow thread. In particular, I used the answer that said to do the following: 

1. Uninstall the app on the emulator
2. `lsof -i :8081` 
3. `kill -9 PID` (where `PID` is the pid value for the open connections on port 8081)
4. Rebuild the android app (`react-native run-android`)

### In Mailchimp, we build email templates and we can send them to Mandrill. In Mandrill, we can edit the template to include custom variables that we can populate from our web apps when we send mail out. How does this work?

In our mandrill template editor (go to Outbound > Templates when you're logged into Mandrill). Click on your template and you'll see the HTML code. Go down to the area where you want to insert a custom variable and put something like

```
<div mc:edit="tutor_name"></div>
```

Of course, `tutor_name` should be changed to the text field you want to use. 

Then in our code, depending on the framework you're using, you'll send some sort of a hash where the key is your custom variable names and the value is the value you want to include in your email template. So, for above, it would be: 

```
{
	tutor_name: "Neeraj Kapoor",
	...
}
```

### After using Keras to build a CNN, the model file generated at a .h5 extension. I tried to convert this in Python to a .tflite file. But when I ran this in my react-native app, it failed. How did I get this to work? 

By using this command to convert our keras file into a `.tflite` file:

`tflite_convert   --output_file=model.tflite   --keras_model_file=model.h5`

This command line tool is installed when you install the Python tensor flow package. 

You can see some examples [here](https://www.tensorflow.org/lite/convert/cmdline_examples). 

