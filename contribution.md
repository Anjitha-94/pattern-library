# Dgl 104 Add react native for singleton pattern

I have read about react native code for solving the issue assigned to me. From that readings I found that react native is a framework in javascript.

This implementation of the React Native Singleton pattern provides a structured way to manage shared state among several application components. It uses a static property to ensure that only one instance of the class is created and to grant global access to its methods and attributes.



### Key Components

* Static Instance Variable (_instance): A private static variable that holds the single instance of the class. This ensures that there is only one instance of the Singleton class throughout the application.

* Constructor: Checks if an instance already exists by examining _instance. If it exists, the constructor returns the existing instance instead of creating a new one. If it doesn't exist, the constructor initializes the class's state and assigns the newly created instance to _instance.

* Static Getter (instance): A static getter method that provides global access to the Singleton instance. It creates the instance if it doesn't exist and returns the existing instance if it does.

* State Management (getState and setState): Methods to get the current state and update the state of the Singleton, respectively. setState merges the new state with the existing state, allowing for updates.

* Object.freeze(): Called on the Singleton class to prevent any future modifications, ensuring the integrity and immutability of the Singleton instance.



## Usage within a React Native Component


The SingletonDemo component demonstrates how to interact with the Singleton class to manage shared state within a React Native application.

Key Components of SingletonDemo:


* Constructor: Accesses the Singleton instance using Singleton.instance and stores it in the component's state. This provides a way to interact with the shared state managed by the Singleton.

* updateSingletonState Method: A method defined to update the state of the Singleton. It demonstrates how to modify shared state across the application through the Singleton instance.

* renderSingletonState Method: This method retrieves the current state from the Singleton and renders it. It illustrates how to access shared state from the Singleton and display it within a component.

* Render Method: Defines the UI of the component, displaying the current state stored in the Singleton and providing a button to trigger a state update. This showcases the Singleton's role in facilitating shared state management across different parts of the application.



### Example with react native code

```
import React, { useState } from 'react';
import { View, Text, Button } from 'react-native';
import MySingleton from './MySingleton'; // Assuming MySingleton.js is in the same directory

const App = () => {
  const singletonInstance = MySingleton.getInstance();
  const [propertyValue, setPropertyValue] = useState(singletonInstance.myProperty);

  const updateSingletonProperty = () => {
    const newValue = propertyValue === "Initial Value" ? "Updated Value" : "Initial Value";
    // Direct property mutation won't work because the instance is frozen
    // Need a method inside the singleton to update its properties if they're meant to change
    console.log(`Attempted to update singleton property to: ${newValue}`);
    // This won't actually update the property because the object is frozen
    singletonInstance.myProperty = newValue;
    // Reflect attempted change in the UI for demonstration
    setPropertyValue(singletonInstance.myProperty);
  };

  return (
    <View style={{flex: 1, justifyContent: 'center', alignItems: 'center'}}>
      <Text>Singleton Property: {propertyValue}</Text>
      <Button title="Update Singleton Property" onPress={updateSingletonProperty} />
      <Button title="Call Singleton Method" onPress={() => singletonInstance.myMethod()} />
    </View>
  );
};
```


## Import Statements

- **React, { useState }**: Imports React and its `useState` hook for managing component state.
- **View, Text, Button**: Imports basic UI components from React Native for layout, displaying text, and user interaction.
- **MySingleton**: Imports the singleton class from `MySingleton.js` to be used in the app.

## The App Component

The `App` function defines a React Native component:

- **Singleton Instance**: `MySingleton.getInstance()` is called to obtain the singleton instance. If an instance doesn't already exist, it will create one; otherwise, it returns the existing instance.
- **useState Hook**: Initializes `propertyValue` state with the `myProperty` property of the singleton instance. This state is used to display the singleton's property value in the UI.

## updateSingletonProperty Function

This function attempts to toggle the singleton's `myProperty` value between "Initial Value" and "Updated Value":

- **Immutability Note**: Direct mutation of `singletonInstance.myProperty` is attempted, but because the instance is frozen (`Object.freeze()`), this operation will not actually update the property. The code's current setup assumes a mutable object, which is not the case due to freezing. A practical approach to mutable properties in a frozen instance would require implementing setter methods within the singleton class.
- **Console Log**: Logs the attempted new value for debugging or demonstration purposes.
- **State Update Attempt**: Attempts to update the local component state `propertyValue` to reflect the change. However, since the actual property on the singleton doesn't change, the displayed value in the UI does not update after the button press.

## The Render Method

Renders the app's UI elements:

- **View Container**: A flex container that centers its children.
- **Text Display**: Shows the current `propertyValue`, initially reflecting `myProperty` from the singleton. Intended to update when `updateSingletonProperty` is executed, but doesn't due to the reasons mentioned above.
- **Buttons**: Two buttons are rendered:
  - "Update Singleton Property" attempts to toggle the singleton's property value. Due to the singleton's immutability, this doesn't work as expected.
  - "Call Singleton Method" triggers `myMethod()` on the singleton instance, which logs a message to the console.

## Summary

This code demonstrates an attempt to interact with a singleton pattern in React Native, showcasing state sharing and method invocation. The attempt to mutate the singleton's property directly from the component doesn't succeed because the singleton is frozen to ensure its singularity and immutability. For such a pattern to work as intended, especially if mutable state is required, the singleton class should provide methods to safely update its properties, ensuring controlled access and mutation of its state.



### Example 2

```
// UserProfileSingleton.js
class UserProfileSingleton {
  static _instance;

  constructor() {
    if (UserProfileSingleton._instance) {
      return UserProfileSingleton._instance;
    }
    this.profile = { username: "defaultUser", email: "user@example.com" };
    UserProfileSingleton._instance = this;
    Object.freeze(UserProfileSingleton._instance);
  }

  static get instance() {
    if (!UserProfileSingleton._instance) {
      UserProfileSingleton._instance = new UserProfileSingleton();
    }
    return UserProfileSingleton._instance;
  }

  getProfile() {
    return this.profile;
  }

  updateProfile(newProfile) {
    // Normally, you wouldn't be able to modify a frozen object, but for the sake of the example,
    // assume this method is meant to communicate with a backend to update the profile.
    // This method would then trigger a re-fetch or update the local state to reflect the new profile data.
    console.log("Profile updated:", newProfile);
  }
}

export default UserProfileSingleton;
```

The application 
```
// ProfileScreen.js
import React, { Component } from 'react';
import { View, Text, Button } from 'react-native';
import UserProfileSingleton from './UserProfileSingleton'; // Assuming UserProfileSingleton.js is in the same directory

export default class ProfileScreen extends Component {
  constructor(props) {
    super(props);
    this.userProfileSingleton = UserProfileSingleton.instance;
    this.state = {
      profile: this.userProfileSingleton.getProfile(),
    };
  }

  updateProfile = () => {
    const newProfile = { username: "newUsername", email: "newuser@example.com" };
    this.userProfileSingleton.updateProfile(newProfile);
    // For this example, we're directly updating the component state
    // In a real scenario, this could trigger a re-fetch or listen for a profile update event
    this.setState({ profile: newProfile });
  };

  render() {
    const { profile } = this.state;
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <Text>Username: {profile.username}</Text>
        <Text>Email: {profile.email}</Text>
        <Button title="Update Profile" onPress={this.updateProfile} />
      </View>
    );
  }
}
```

# `ProfileScreen.js` Code Explanation

The `ProfileScreen.js` file defines a React Native component that interacts with a singleton pattern through the `UserProfileSingleton` class. This component demonstrates how to display and update user profile information using a shared instance of the singleton.

## Import Statements

- **React, { Component }**: Imports React and its component class to allow the creation of a class-based React component.
- **View, Text, Button**: Imports UI components from React Native. `View` is used as a container, `Text` to display text, and `Button` for user interaction.
- **UserProfileSingleton**: Imports the singleton class from a local file. This class is assumed to manage user profile information in a single instance throughout the application.

## Class Definition: `ProfileScreen`

- Extends `Component` to create a React class component. Class components can hold state and provide more complex UI logic and lifecycle methods.

## Constructor and State Initialization

- The constructor is a special method for creating and initializing objects created with a class.
- `super(props)` calls the constructor of the parent class, `Component`, to ensure the component is initialized properly.
- `this.userProfileSingleton = UserProfileSingleton.instance`: Retrieves the singleton instance of the `UserProfileSingleton` class. This instance will be used to access and modify the user's profile data.
- `this.state = { profile: this.userProfileSingleton.getProfile(), }`: Initializes the component's state with the profile data from the singleton. State in React is used for properties of the component that can change, with every change leading to a re-render of the component.

## `updateProfile` Method

- Defined as an arrow function to ensure `this` is bound correctly. Arrow functions do not have their own `this` context, so `this` refers to the enclosing lexical context, which is the `ProfileScreen` class in this case.
- Creates a new profile object and uses the singleton's `updateProfile` method to simulate an update. This is for demonstration, as in a real application, updating the profile might involve API calls.
- Calls `this.setState({ profile: newProfile })` to update the component's state with the new profile information, triggering a re-render to display the updated data.

## Render Method

- Renders the UI components to display the user's profile information and provides a button to update it.
- The `View` component acts as a container with styling to center its children.
- Two `Text` components display the current username and email from the component's state.
- A `Button` component, when pressed, calls the `updateProfile` method to update the profile information.

## Summary

This code exemplifies a React Native component utilizing a singleton pattern to manage and display user profile data. The singleton ensures that the profile data is consistent across the application, while the component itself focuses on rendering the UI and handling user interactions to update the profile. This pattern aids in maintaining a central point of access and modification for shared data, such as user profiles, within an app.
