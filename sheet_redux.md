### Instalación

npm install react-redux
yarn add react-redux
npm install @reduxjs/toolkit react-redux

### Como utilizarlo

#### index.js

```javascript
import {Provider} from 'react-redux';
import store from './src/redux/store';

const MyComponent = () => (
  <Provider store={store}>
    <App />
  </Provider>
);
AppRegistry.registerComponent(appName, () => MyComponent);

```

### En el store.js

```javascript

import {createStore, combineReducers, applyMiddleware} from 'redux';
import reduxThunk from 'redux-thunk';
import expenseReducer from './reducers/expenseReducer';

const appReducer = combineReducers({
  expenseReducer,
});

const store = createStore(appReducer, applyMiddleware(reduxThunk));
export default store;


```

### En el reducer.js

```javascript
import {ActionTypes} from '../constants/expenseTypes';

import expenses from '../../data/expenses';

const initialState = {
  expenses: expenses,
};

export default (state = initialState, action) => {
  switch (action.type) {
    case ActionTypes.MY_ACTION_NAME:
      return {...state} // retornar copia del estado cambiado
    default:
      return state;
  }
};

```

### En el action.js
```javascript
import {ActionTypes} from '../constants/expenseTypes';

export function myAction() {
  return {
    type: ActionTypes.MY_ACTION_NAME,
  };
}

```

```javascript
import {ActionTypes} from '../constants/expenseTypes';

export function myAction() {
  return async (dispatch, state) => {
    return dispatch({
      type: ActionTypes.MY_ACTION_NAME,
    });
  }
}

```


### En el constants.js

```
export const ActionTypes = {
  MY_ACTION_NAME: 'MY_ACTION_NAME',
};

```

####  Acceder al estado dentro de la componente

```javascript
import {connect} from 'react-redux';


const ExpenseListScreen = ({expenses, navigation}) => { 
    return (<View> { /* Mas elementos */ }  </View>)
}

const mapStateToProps = state => {
  const {expenseReducer} = state;
  return {
    expenses: expenseReducer.expenses,
  };
};

const actionsCreators  = {
  actionFunction
}

export default connect(mapStateToProps, actionsCreators)(ExpenseListScreen);

```
