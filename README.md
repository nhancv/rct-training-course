## React Native Training

### Overview

- Create new standard rct project
- Understand Files Structure
- Change Images
- Change Fonts
- Work with I18n
- Update Splash Screen
- Change App Icon
- Understand App Navigation
- Understand Props & States
- Understand Lifecycle of Component
- Work with Redux
- Understand App Version and Env
- Understand App Module

### Steps

#### New project

```
beesight rct

+ Folder name: RctTraining
+ App name (default): BeeSightSoftRCT
+ App id (default): com.beesightsoft.rct

cd RctTraining
npm i
beesight avd
react-native run-android
```

#### Understand Files Structure
#### Change Images

Put snipcode below to `<View style={MainStyles.center}>` in `App/Containers/Main/Main.Screen.js`, then reload

```
  <Image style={{ width: 50, height: 50 }} source={require('../../Images/launch-icon.png')} />
  <Image
    style={{ width: 50, height: 50 }}
    source={{ uri: 'http://icons.iconarchive.com/icons/paomedia/small-n-flat/1024/sign-check-icon.png' }}
  />
  <Image
    style={{ width: 50, height: 50 }}
    source={{ uri: 'https://facebook.github.io/react-native/docs/assets/favicon.png' }}
  />
  <Image
    style={{ width: 66, height: 58 }}
    source={{
      uri:
        'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg=='
    }}
  />
```

#### Change Fonts

Put `resources/IndieFlower.ttf` to `App/Fonts/`, then update `fontFamily` in Text style. 
Then run `react-native link`. Restart app `react-native run-android`

```
<Text style={{
                fontFamily: "Roboto-Light"
                }}>This is main</Text>
```
to
```
<Text style={{
                fontFamily: "IndieFlower"
                }}>This is main</Text>
```

- Work with I18n

Put snipcode below to `App/I18n/languages/vi.json`

```
{
  "welcome": "Xin chào"
}
```

Then, update `App/Containers/Main/Main.Screen.js`. Reload app

```
import Config from 'react-native-config'
import I18n from '../../I18n/'
// I18n.initLanguage('vi')

export default class MainScreen extends Component {
  state = {
    locale: I18n.locale.substr(0, 2)
  }

  toogleLanguage = () => {
    let lang
    if(this.state.locale.indexOf("vi") > -1) {
      lang = 'en'
    } else {
      lang = 'vi'
    }
    I18n.initLanguage(lang)
    this.setState({locale: lang})
  }

  render() {
    return (
    	....

    	            <View style={MainStyles.center}>
    	              ....

              		  <Text>{I18n.t('welcome')}</Text>
		              <Button light
		                onPress={() => {
		                  this.toogleLanguage()
		                }}
		              >
		                <Text> I18n - {this.state.locale} </Text>
		              </Button>
		            .....
		....
		)
	}
}

```

#### Update Splash Screen

For android, update `android/app/src/main/res/layout/launch_screen.xml`. Place snipcode below to inside `LinearLayout`, after `ImageView` block. Restart app `react-native run-android`

```
  <TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
	android:layout_marginTop="16dp"
    android:layout_gravity="center"
    android:text="Hello"
	android:textColor="#25383C"
	android:textSize="18sp"/>
```

#### Change app icon

Make sure app-icon tool installed 
```
brew install imagemagick 
npm install -g app-icon
```

Put `resources/icon.png` (Prepare icon png image at least 192px square) to root dir. Then run `app-icon generate`. Restart app `react-native run-android`

#### Understand App Navigation

- Create new folder `App/Containers/Demo`. 
- In `Demo` folder created, create new file `Demo.Screen.js`
```
import React, {Component} from 'react'
import { Dimensions, ScrollView, Text, Image, View, StyleSheet } from 'react-native'

export default class DemoScreen extends Component {

  render() {
    return (
      <Text>Demo</Text>
    );
  }
}
```
- Declare navigation in `App/Navigation/AppNavigation.js`
```
...
import MainScreen from '../Containers/Main/Main.Screen'
import DemoScreen from '../Containers/Demo/Demo.Screen'
...
// Manifest of possible screens
const PrimaryNav = StackNavigator({

  MainScreen: {screen: MainScreen},
  DemoScreen: {screen: DemoScreen}

...
```
- Test navigate in `App/Containers/Main/Main.Screen.js`

```
...
<View style={MainStyles.center}>
  <Button onPress={() => {
    this.props.navigation.navigate('DemoScreen', {})
  }}><Text>Click Me! </Text></Button>

...
```
- Reload app

#### Understand Props & States
#### Understand Lifecycle of Component
#### Work with Redux
- Step 1: Define Service at `App/Containers/Demo/Demo.Api.js`
```
import apisauce from 'apisauce'

const create = (baseURL = 'https://api.github.com/') => {
  const api = apisauce.create({
    baseURL,
    headers: {
      'Cache-Control': 'no-cache'
    },
    timeout: 10000
  })
  const getUser = username => api.get('search/users', { q: username })

  //Fake data
  const getFakeData = () => {
    return 'Fake data'
  }

  return {
    getFakeData,
    getUser
  }
}

export default {
  create
}

```
- Step 2: Define Action at `App/Containers/Demo/Demo.Action.js`
```
import { createActions } from 'reduxsauce'

/* ------------- Types and Action Creators ------------- */
const { Types, Creators } = createActions({
  fakeDataRequest: [],
  restApiRequest: ['username'],
  success: ['data'],
  failure: ['error']
})

export const DemoAction = Creators
export const DemoActionCode = Types

```
- Step 3: Define Reducer at `App/Containers/Demo/Demo.Reducer.js`
```
import { call, put } from 'redux-saga/effects'
import { createReducer } from 'reduxsauce'
import Immutable from 'seamless-immutable'

// Import Action and Api services
import { DemoAction, DemoActionCode } from './Demo.Action'
import Api from './Demo.Api'
// Export ActionCode and Services
export default DemoActionCode
export const DemoServices = Api.create()

/* ------------- Business logic ------------- */
export const DemoLogicFunc = {
  getFakeData,
  getRestData
}

function* getFakeData(api, action) {
  const response = yield call(api.getFakeData)
  yield put(DemoAction.success(response))
}

function* getRestData(api, action) {
  const { username } = action
  const response = yield call(api.getUser, username)
  if (response.ok && response.status === 200) {
    yield put(DemoAction.success(JSON.stringify(response.data)))
  } else {
    yield put(DemoAction.failure(JSON.stringify(response.data)))
  }
}

/* ------------- Initial State ------------- */
const INITIAL_STATE = Immutable({
  data: null,
  isFetching: false,
  error: false
})

/* ------------- Hookup Reducers To Types ------------- */
export const reducer = createReducer(INITIAL_STATE, {
  [DemoActionCode.FAKE_DATA_REQUEST]: (state, action) => {
    return {
      ...state,
      data: null,
      isFetching: true,
      error: false
    }
  },
  [DemoActionCode.REST_API_REQUEST]: (state, action) => {
    return {
      ...state,
      data: null,
      isFetching: true,
      error: false
    }
  },
  [DemoActionCode.SUCCESS]: (state, action) => {
    return {
      ...state,
      data: action.data,
      isFetching: false,
      error: false
    }
  },
  [DemoActionCode.FAILURE]: (state, action) => {
    return {
      ...state,
      data: action.error,
      isFetching: false,
      error: true
    }
  }
})

```
- Step 4: Combine Redux Flow

+ File: `App/Redux/index.js`
```
/* ------------- Assemble The Reducers ------------- */
export const reducers = combineReducers({
  ....
  search: require('./SearchRedux').reducer,
  demo: require('../Containers/Demo/Demo.Reducer').reducer
  /* Reducer here */
})
```
+ File: `App/Sagas/index.js`
```
/* ------------ REDUX ------------ */
import DemoActionCode, { DemoLogicFunc, DemoServices} from '../Containers/Demo/Demo.Reducer'

.....

	// some sagas receive extra parameters in addition to an action
    takeLatest(GithubTypes.USER_REQUEST, getUserAvatar, api),

    // Demo redux flow
    takeLatest(DemoActionCode.REST_API_REQUEST, DemoLogicFunc.getRestData, DemoServices),
    takeLatest(DemoActionCode.FAKE_DATA_REQUEST, DemoLogicFunc.getFakeData, DemoServices)
.....

```
- Step 5: Bind to Screen at `App/Containers/Demo/Demo.Screen.js`
```
import React, { Component } from 'react'
import { Dimensions, ScrollView, Text, Image, View, StyleSheet } from 'react-native'
import { Container, Header, Left, Body, Right, Button, Icon, Title, Content } from 'native-base'

import { connect } from 'react-redux'
import { DemoAction } from './Demo.Action'

export class DemoScreen extends Component {
  render() {
    return (
      <Content>
        <Button transparent onPress={() => this.props.navigation.goBack()}>
          <Icon name="arrow-back" />
        </Button>
        <Button onPress={() => this.props.getFakeData(DemoAction.fakeDataRequest())}>
          <Text>Test data</Text>
        </Button>
        <Button onPress={() => this.props.getGithubUser(DemoAction.restApiRequest('nhancv'))}>
          <Text>Fetch user</Text>
        </Button>

        {this.props.isFetching ? (
          <Text>Fetching...</Text>
        ) : (
          <View>
            <Text>Data: {this.props.data}</Text>
            {this.props.error ? <Text>Has error</Text> : null}
          </View>
        )}
      </Content>
    )
  }
}

function mapStateToProps(state) {
  return {
    isFetching: state.demo.isFetching,
    data: state.demo.data,
    error: state.demo.error
  }
}

function mapDispatchToProps(dispatch) {
  return {
    getFakeData: request => dispatch(request),
    getGithubUser: request => dispatch(request)
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(DemoScreen)

```


#### Understand App Version and Env

- Update new version by running 
```
# List current version
npm version 
# New version
npm version <new version>
```
- Run with different Env. First create new environment file `.env.ENVIRONMENT_NAME`. Ex: `.env.staging`, `.env.live`, ... Then running
```
ENVIRONMENT=staging
export ENVFILE=.env.${ENVIRONMENT} && react-native run-android
```

Look the new env updated on toolbar.

#### Understand App Module















