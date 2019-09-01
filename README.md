### Redux Boiler Plate

Set up basic react app with redux

This is courtesy of a reduxCrashCourse tutorial from Brian Traversy https://www.youtube.com/watch?v=93p3LxR9xfM

Please support him by subscribing to his channel - his tutorials are very good!

### Getting Started from the Command Line

#### There are 7 steps:

1. Use create-react-app name_of_app and cd into it
2. Install redux, react-redux, and redux-thunk via npm
3. cd into src and add a store.js file.
4. Make an actions directory.
5. cd into actions directory make two files - types.js and postActions.js then cd out
6. Make a reducers directory.
7. cd into reducers directory make two files - index.js and postReducer.js then cd back to root

```
create-react-app reduxbp
cd reduxbp
npm i redux react-redux redux-thunk
cd src
touch store.js
mkdir actions
cd actions
touch types.js postActions.js
cd ../
mkdir reducers
cd reducers
touch index.js postReducer.js
cd ../
cd ../
```

### Setting up boiler plate for redux files

#### There are 5 steps:

1. Open your project in your text editor of choice
  * Click on store.js file then type:
  ```javascript

import { createStore, applyMiddleware, compose } from "redux";
import thunk from "redux-thunk";
import rootReducer from "./reducers";

const initalState = [];
const middleware = [thunk];
const store = createStore(
  rootReducer,
  initalState,
  compose(
    applyMiddleware(...middleware),
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
  )
);

export default store;
      ```
    * this sets up a store and connects it to chrome redux dev tools so you can debug easier
    * basiclly - your store is the go between for your app - it can take in actions and feed\n reducers with initial state and the action and recieve updated state which it then feeds to components
    * Hit command + s to save
2. Click types.js in actions folder and type:
      ```javascript

export const FETCH_POSTS = "FETCH_POSTS";
export const NEW_POST = "NEW_POST";
      ```
  * This is to set up a boiler plate types to fetch posts and to create a new post
  * Hit command + s to save
3. Click postActions.js in actions folder and type:
```javascript

import { FETCH_POSTS, NEW_POST } from "./types";

export const fetchPosts = () => dispatch => {
  console.log("fetching");
  fetch("https://jsonplaceholder.typicode.com/posts")
    .then(res => res.json())
    .then(posts =>
      dispatch({
        type: FETCH_POSTS,
        payload: posts
      })
    );
};

export const createPost = postData => dispatch => {
  console.log("createPost action called");
  fetch("https://jsonplaceholder.typicode.com/posts", {
    method: "POST",
    headers: {
      "content-type": "application/json"
    },
    body: JSON.stringify(postData)
  })
    .then(res => res.json())
    .then(post =>
      dispatch({
        type: NEW_POST,
        payload: post
      })
    );
};

      ```
  * Basically this is the dispatching of actions to the store - in this case, this is boiler plate using fake rest calls from https://jsonplaceholder.typicode.com/
  * In this example there are two post actions - fetch and new
  * Hit command + s to save
4. Click index.js in reducers folder and type:
```javascript

import { combineReducers } from "redux";
import postReducer from "./postReducer";

export default combineReducers({
  posts: postReducer
});


      ```
  * Basically this is the rootReducer which will combine the reducers. In this case, there is only one reducer.
  * Hit command + s to save
5. Click postReducer.js in reducers folder and type:
```javascript

import { FETCH_POSTS, NEW_POST } from "../actions/types";

const initialState = {
  items: [],
  item: {}
};

export default function(state = initialState, action) {
  switch (action.type) {
    case FETCH_POSTS:
      console.log("fetch reducer");
      return {
        ...state,
        items: action.payload
      };
    case NEW_POST:
      console.log("new reducer");
      return {
        ...state,
        item: action.payload
      };
    default:
      return state;
  }
}

      ```
  * This is the postReducer which will take in state and action from the store.
  * It will use this information to return the updated state based on the action.type and action.payload that was fed to the store from the dispatch function of the actions.
  * Hit command + s to save

### Getting react components to communicate with redux

#### For the boiler plate example we will have 2 components
1. cd into src and make a components folder and cd into it
2. Create Posts.js and Postform.js files and cd back to root
```
cd src
mkdir components
cd components
touch Posts.js Postform.js
cd ../
cd ../
```
3. Click App.js and overwrite the existing code with:
```javascript

import React from "react";
import logo from "./logo.svg";
import "./App.css";
import { Provider } from "react-redux";

import Posts from "./components/Posts";
import Postform from "./components/Postform";

import store from "./store";

function App() {
  return (
    <Provider store={store}>
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <a
            className="App-link"
            href="https://reactjs.org"
            target="_blank"
            rel="noopener noreferrer"
          >
            Learn React
          </a>
        </header>
        <Postform />
        <hr />
        <Posts />
      </div>
    </Provider>
  );
}

export default App;


      ```
  * The main reduxy thing is the Provider wrapper around the main div element which has the store as props.
  * The main imports are the Provider and store, as well as the components that will be the basic post app.
  * Hit command + s to save
4. Click Posts.js in components folder and type:
```javascript

import React, { Component } from "react";
import PropTypes from "prop-types";
import { connect } from "react-redux";
import { fetchPosts } from "../actions/postActions";

class Posts extends Component {
  componentWillMount() {
    this.props.fetchPosts();
  }

  componentWillReceiveProps(nextProps) {
    if (nextProps.newPost) {
      this.props.posts.unshift(nextProps.newPost);
    }
  }

  render() {
    const postItems = this.props.posts.map(post => (
      <div key={post.id}>
        <h3>{post.title}</h3>
        <p>{post.body}</p>
      </div>
    ));
    return (
      <div>
        <h1>Posts</h1>
        {postItems}
      </div>
    );
  }
}

Posts.propTypes = {
  fetchPosts: PropTypes.func.isRequired,
  posts: PropTypes.array.isRequired,
  newPost: PropTypes.object
};

const mapStateToProps = state => ({
  posts: state.posts.items,
  newPost: state.posts.item
});

export default connect(
  mapStateToProps,
  { fetchPosts }
)(Posts);

      ```
  * This is the main component which also includes the form component element.
  * Note that state is not used but props are use instead and the state is mapped to props in a funtion - the state will come from the store which is provided by the Provider wrapper in the App.js file.
  * The wiring of the component to redux is fairly complicated - you need to import connect, proptypes and the fetch action.
  * Note: you don't export the class directly but do it via the connect function.
  * Hit command + s to save
5. Click Postform.js in components folder and type:
```javascript

import React, { Component } from "react";
import PropTypes from "prop-types";
import { connect } from "react-redux";
import { createPost } from "../actions/postActions";

class Postform extends Component {
  constructor(props) {
    super(props);
    this.state = {
      title: "",
      body: ""
    };

    this.onChange = this.onChange.bind(this);
    this.onSubmit = this.onSubmit.bind(this);
  }

  onChange(e) {
    this.setState({ [e.target.name]: e.target.value });
  }

  onSubmit(e) {
    e.preventDefault();

    const post = {
      title: this.state.title,
      body: this.state.body
    };

    // Call action to create a post
    this.props.createPost(post);
  }
  render() {
    return (
      <div>
        <h1>Add Post</h1>
        <form onSubmit={this.onSubmit}>
          <div>
            <label>Title: </label>
            <br />
            <input
              type="text"
              name="title"
              onChange={this.onChange}
              value={this.state.title}
            />
          </div>
          <br />
          <div>
            <label>Body: </label>
            <br />
            <textarea
              name="body"
              onChange={this.onChange}
              value={this.state.body}
            />
          </div>
          <br />
          <button type="submit">Submit</button>
        </form>
      </div>
    );
  }
}

Postform.propTypes = {
  createPost: PropTypes.func.isRequired
};

export default connect(
  null,
  { createPost }
)(Postform);

      ```
  * This is the form component 
  * The wiring of the component to redux is fairly complicated - you need to import connect, proptypes and the create action.
  * Note: unlike with the fetch action, this component can use state b/c it is feeding this state as props to action dispatch.
  * Note: you don't export the class directly but do it via the connect function.
  * Hit command + s to save

### Minor styling
#### Modify App.css
Click on App.css
Replace .App styling with:
```css

.App {
  width: 90%;
  margin: auto;
}
      ```
### Run app
In command line from root type:
```
npm start
```