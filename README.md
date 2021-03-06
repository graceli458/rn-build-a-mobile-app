# Build a Mobile App with React Native

This workshop has been arranged for [DevFest '20](https://2020.devfe.st), but feel free to use it any way you like.

[Click here for the Accompanying Slides](https://tinyurl.com/devfest20rn)

## Introduction

In this workshop, we'll develop a simple app where you can create study locations at Columbia on a map! Let's call this app StudyBuddy, and here's how it'll look on an iPhone X in the end:

<img src="assets/preview.png" style="height: 400px;" />

<hr/>

## Setup

1. Make sure you have [git](https://git-scm.com/) and [Node.js](https://nodejs.org/en/) installed on your computer. Follow the above links to install.<br/><br/>
2. <img src="https://hackernoon.com/drafts/xa1cw305h.png" width="200px"/><br/> Expo is an awesome tool for creating React Native apps on the fly! Download the CLI tool using this command: `npm install -g expo-cli`. Then, download the client app on your mobile phone through the [App Store](https://apps.apple.com/us/app/expo-client/id982107779) or [Google Play](https://play.google.com/store/apps/details?id=host.exp.exponent&hl=en_US). You'll be prompted to create an account, and please do! <br/><br/>
3. Go into Terminal or Command Prompt and `cd` to the directory of your choice. Then, clone the starter code using the following command: `git clone https://github.com/adicu/rn-build-a-mobile-app.git`.<br/><br/>
4. Finally, navigate to this directory using `cd rn-build-a-mobile-app` and then run `npm install` to install this project's dependencies. (If you're on Windows, you'll likely have to use 'Node.js Command Prompt' instead of the regular command prompt.) You're now ready to code!<br/><br/>

## Pre-Coding Notes

- To run this program, simply run `npm start` from the project directory. This command will open a new browser window with the Expo console. From there (and ensuring you have the Expo Client installed on your phone), simply capture the QR code on your phone and have it redirect to the client app.<br/>
<img src="assets/expo.png" style="width: 400px;" /><br/>
However, the starter code comes incomplete and so, if your app loads successfully, you'll see a blank screen. That's good; keep the app running on your phone so you can see your changes as you code. If your app ever crashes or freezes, simply press `Ctrl+C` to close the terminal and repeat this step.<br/><br/>
- To expedite this workshop, a lot of code has been written for you, specifically under `src/components`, `src/constants`, and `src/util`. No need to touch any code here.<br/><br/>

## Exploration

1. Navigate to `App.tsx` and explore it a little bit. It's very short and all it does is, you guessed it, render the `<MainScreen>` component to the top of the navigation stack. The `render()` function is the most important function inherited from the `Component` React class. By putting the `MainScreen` component in a [JSX/TSX](https://www.typescriptlang.org/docs/handbook/jsx.html) (for TypeScript) tag, you are shorthandedly invoking its `render()` function this way.<br/><br/>
2. Now, navigate to `src/screens/MainScreen.tsx`. For now, this component is pretty empty, but it'll soon house all the functionality we need.<br/><br/>
3. Finally, navigate to `src/interfaces/StudyZone.ts`. By using TypeScript instead of JavaScript, we're provided with the advantage of using typed interfaces when storing and retrieving objects. We'll create the `StudyZone` interface first.<br/><br/>

## Let's Code It

1. Navigate to the `StudyZone` interface file (`src/interfaces/StudyZone.ts`). We want a way to store and retrieve location information when we view our study map. The first thing we'll need is obviously a `location` property, and we'll use the `LatLng` class from [react-native-maps](https://github.com/react-native-community/react-native-maps) to our benefit. Next, we'll add a simple `name` string to name our locations. Our completed `StudyZone` interface should look as follows (make sure to save your work):
```
import { LatLng } from 'react-native-maps'

export interface StudyZone {
    name:string
    location:LatLng
}
```


2. Now, navigate back to `src/screens/MainScreen.tsx`. In the `render()` function, return an empty `<View>`. This will be our outer container.
```
public render = ():ReactNode => {
    return (
        <View>
            // Content goes here...
        </View>
    )
}
```

3. Inside the `<View>`, let's add a simple navigation bar that says the name of our app. Add the following into where it says `// Content goes here...`. You might be wondering a few things here, and here are the answers.
- You'll notice the `style` property has a value of `styles.navigationBar`. This points to the empty `styles` object at the bottom of the file, and we'll add the `navigationBar` property to this object to make the navigation bar look pretty.
- Secondly, the `centerContent` property is one unique to the `NavigationBar` component and contains a list of components to display at the center of the bar. In React and React Native, you have to add a `key` property to each component in a list of components so the renderer can distinguish each individual component in the list (more info [here](https://reactjs.org/docs/lists-and-keys.html)).
- Finally, we add a `<Text>` component with our app name and the style of `navigationBarText`, which we'll add in the next step.
```
<NavigationBar
    style={styles.navigationBar}
    centerContent={[
        <View key={0}>
            <Text style={styles.navigationBarText}>
                STUDY BUDDY
            </Text>
        </View>
    ]}
/>
```
4. To style our navigation bar and the text inside it, add the following properties to the `styles` object at the bottom of `src/screens/MainScreen.tsx`. We'll use the global `colors` object from `src/constants/colors` to retrieve color hex values. Additionally, the `DeviceUtil` class provides custom scaling functions that allow us to size our fonts and components according to the size of our devices' screens.
```
const styles = StyleSheet.create({
    navigationBar: {
        backgroundColor: colors.quaternary,
    },
    navigationBarText: {
        color: colors.light,
        fontWeight: '900',
        textAlign: 'center',
        fontSize: DeviceUtil.scaleFont(18),
    },
})
```
5. Even though its simple, the navigation bar seems to take up a lot of space in our `render()` function. We can actually move this component to its own [arrow function]() under the `MainScreen` class in the same file as follows, and **ALSO** replace the `<NavigationBar>` component declaration in `render()` with simply `<MainNavigationBar />`:
```
const MainNavigationBar = () => (
    <NavigationBar
        style={styles.navigationBar}
        centerContent={[
            <View key={0}>
                <Text style={styles.navigationBarText}>
                    STUDY BUDDY
                </Text>
            </View>
        ]}
    />
)
```
6. Next, let's set a few `state` variables up for future use. In TypeScript, classes can have property variables like in any other language, and you can make them `public` or `private`. React components have a `public state` variable that is reserved for storing information that is crucial to the front-end of a component. `state` properties must only ever be updated with the `this.setState({ key: value })` function and, when this function is called, the ENTIRE component is reloaded to express the value change. We'll store three important keys now. Firstly, the `state.region` will contain Columbia's coordinates. `state.studyZones` will contain a list of study zones to display and `state.isDialogOpen` will control opening and closing a prompt dialog where the user can enter the name of their study zone. Put the following at the top of the `MainScreen` class.
```
public state = {
    region: {
        latitude: 40.807411,
        longitude: -73.962123,
        latitudeDelta: DISTANCE_DELTA,
        longitudeDelta: DISTANCE_DELTA,
    } as Region,
    studyZones: [] as StudyZone[],
    isDialogOpen: false as boolean,
}
```
7. We'll be saving study zones and loading them from your device's memory, and we luckily have a util class that'll help us with that. Write the following function, `componentDidMount()`. Like `render()`, this function is reserved in React and React Native and is called immediately after a component is loaded. Within it, we'll use the `StorageUtil` to retrieve a list of study zones from the device using the `STUDY_ZONE_KEY`. Write this function above `render()`:
```
public componentDidMount = () => {
    // Load study zones from storage after the screen loads
    StorageUtil.get(STUDY_ZONE_KEY).then((studyZones:StudyZone[]|null) => {
        if (!studyZones) {
            studyZones = []
            StorageUtil.set(STUDY_ZONE_KEY, studyZones)
        }
        this.setState({ studyZones })
    })
}
```
8. Time to add the map! Below the `<MainStackNavigationBar />`, add the following `<MapView>` component. There's a lot going on here, so let's distill almost every line, one by one.
- `style={styles.map}`: Add the following `map` style as we did earlier in the `styles` object:
```
map: {
    width: DeviceUtil.deviceWidth(),
    height: DeviceUtil.deviceHeight(),
},
```
- `region={this.state.region}`: This points to the region we have stored in the `state` variable. The map will automatically load these coordinates on load.
- `onDoublePress={this.promptCreateStudyZone}`: When the user double-taps the map in a certain location, we'll call the method `promptCreateStudyZone` to show a prompt that asks them for the name of their new study zone before creating it.
- `mapType={'hybrid'}`: Shows a mix of satellite-imaged buildings and simple shapes.
- `showsUserLocation={true}`: Adds a dot on the map to show the user's location.
- `loadingEnabled={true}`: Shows a loading indicator when the map is loading.
- `showsPointsOfInterest={false}`: Prevents showing "point of interest" locations on the map.
- `loadingBackgroundColor={colors.secondary}` Sets the background color of the loading page to the `secondary` color constant.
- `loadingIndicatorColor={colors.light}`: Sets the loading indicator color to the `light` color constant.
- (everything inside `<MapView>`): loops through `this.state.studyZones` and creates a map `<Marker>` component to show the created study zones on the map.
```
<MapView
    style={styles.map}
    region={this.state.region}
    onDoublePress={this.promptCreateStudyZone}
    mapType={'hybrid'}
    showsUserLocation={true}
    loadingEnabled={true}
    showsPointsOfInterest={false}
    loadingBackgroundColor={colors.secondary}
    loadingIndicatorColor={colors.light}
>
    {this.state.studyZones.map((studyZone:StudyZone, index:number) => (
        <Marker
            key={index}
            coordinate={studyZone.location}
            title={studyZone.name}
        />
    ))}
</MapView>
```
9. Before writing the function handlers that are triggered by different interactions in our app, let's add an input dialog prompt that pops up when `this.state.isDialogOpen` is true. When the user taps the "submit" button, the value is sent as the first argument to `this.createStudyZone` before calling it immediately. We will also trigger the `toggleDialog(...)` function we haven't written yet to close the dialog. Put the following under the `<MapView>` component:
```
<DialogInput isDialogVisible={this.state.isDialogOpen}
    title={"New Study Zone"}
    message={"Type a name for your new study zone."}
    hintInput ={"Butler"}
    submitInput={this.createStudyZone}
    closeDialog={() => { this.toggleDialog(false) }}>
</DialogInput>
```
10. Let's create the `toggleDialog` function that simply sets the `isDialogOpen` property of our `state` variable in order to show/hide the dialog. Write the following under `createStudyZone`:
```
private toggleDialog = (isDialogOpen:boolean):void => {
    this.setState({ isDialogOpen })
}
```
11. Now, let's start to write the function handlers. First, we need to write the function that triggers the dialog that prompts the user for the name of their study zone. It takes in an event fired by the map when the user double taps it, parses the exact coordinates the user had tapped, and sets the `studyZoneLocation` class property. Put the following under `render()`:
```
private promptCreateStudyZone = (event:MapEvent):void => {
    const coordinate:LatLng = event.nativeEvent.coordinate
    this.studyZoneLocation = coordinate
    this.toggleDialog(true)
}
```
12. You may have noticed that we set the `studyZoneLocation` class property before even defining it. Add this to the top of the `MainScreen.tsx` file:
```
private studyZoneLocation!:LatLng
```
13. Now, let's instantiate the `createStudyZone` function that's called when the user taps the submit button in the dialog. Note that the name is passed in as an argument, as mentioned previously, and so all we need to do is update the list of study zones both locally (in the component) and in storage. Finally, we'll toggle the dialog to off.
```
private createStudyZone = (name:string):void => {
    const newStudyZone = {
        name,
        location: this.studyZoneLocation
    }
    const studyZones = this.state.studyZones.concat([ newStudyZone ])
    this.setState({ studyZones })
    StorageUtil.set(STUDY_ZONE_KEY, studyZones)
    this.toggleDialog(false)
}
```

## We're done... kinda

Reload your app in Expo (recall using `npm start` and capturing the QR code on your phone). You should be able to double tap anywhere on the map and create new study zones! Otherwise, figure out what's going wrong—–[StackOverflow](https://stackoverflow.com) is your best friend here.

We're finally done! Just kidding. We have the functionality to create study zones now, but what about **deleting** them? Let's implement this functionality next.

## Deleting Study Zones

1. Once again, for the sake of brevity, we'll implement the functionality to delete all study zones on our map. Firstly, we should display a button to perform this action *only* if the user has already created at least one study zone. This can be accomplished by [short-circuiting]() the button we'll be creating by prepending the `this.state.studyZones.length > 0` condition. To implement the button container, we'll be using the [TouchableOpacity](https://facebook.github.io/react-native/docs/touchableopacity) component, which has an `onPress` handler. Add the following between `<MainNavigationBar />` and `<MapView>`, and don't forget the braces:
```
{
    this.state.studyZones.length > 0 &&
    <TouchableOpacity style={styles.deleteButton} onPress={this.deleteStudyZones}>
        <Text style={styles.deleteButtonText}>
            Delete All Study Zones
        </Text>
    </TouchableOpacity>
}
```
2. Next, let's style the button! You know what to do:
```
deleteButton: {
    zIndex: 1,
    position: 'absolute',
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: colors.primary,

    top: DeviceUtil.statusBarHeight() + DeviceUtil.scale(60),
    left: DeviceUtil.scale(30),
    right: DeviceUtil.scale(30),

    padding: DeviceUtil.scale(12),
},
deleteButtonText: {
    color: colors.light,
    fontWeight: '900',
    textAlign: 'center',
    fontSize: DeviceUtil.scaleFont(18),
},
```
3. Finally, let's create the `deleteStudyZones` handler that'll remove the study zones from `state` and local storage. Put the following at the end of the `MainScreen` class.
```
private deleteStudyZones =() => {
    StorageUtil.remove(STUDY_ZONE_KEY)
    this.setState({ studyZones: [] })
}
```
4. And... we're done! Once again, test your app out with `npm start` if it isn't already running. Congrats on taking your first step in becoming a React Native beast.


<hr/>

## Presented By

### Anthony Krivonos
[Portfolio](https://anthonykrivonos.com/) | [GitHub](https://github.com/anthonykrivonos)