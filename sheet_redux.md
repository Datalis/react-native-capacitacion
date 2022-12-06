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

```
import {ActionTypes} from '../constants/expenseTypes';

import expenses from '../../data/expenses';

const initialState = {
  expenses: expenses,
};

export default (state = initialState, action) => {
  switch (action.type) {
    case ActionTypes.ADD_EXPENSE:
      console.log('ejecutaondo accion add');
      return {...state, expenses: [...state.expenses, action.data]};
    default:
      return state;
  }
};

```

### En el action.js
```
import {ActionTypes} from '../constants/expenseTypes';

export function addExpense(item) {
  return {
    type: ActionTypes.ADD_EXPENSE,
    data: item,
  };
}

```

### En el constants.js

```
export const ActionTypes = {
  ADD_EXPENSE: 'ADD_EXPENSE',
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
export default connect(mapStateToProps)(ExpenseListScreen);

```