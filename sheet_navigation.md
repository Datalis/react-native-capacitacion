### Instalación 

> Este sheet está basado en la versión 5, para utilizar la última versión consultar [documentación](https://reactnavigation.org/docs/5.x/getting-started)

Instalar funcionalidades comunes a todos los *navigators*
`npm install @react-navigation/native@^5.x`

#### Otras dependencias

`npm install react-native-reanimated react-native-gesture-handler react-native-screens react-native-safe-area-context @react-native-community/masked-view`


### Uso

```javascript
    import 'react-native-gesture-handler'; 
    import * as React from 'react';
    import { NavigationContainer } from '@react-navigation/native';

    export default function App() {
        return (
            <NavigationContainer>{/* Rest of your app code */}</NavigationContainer>
        );
    }
```

**NavigationContainer**: Encargado de mantener el estado de la navegación (historia, rutas, etc), todo el código de nuestra aplicación debe estar dentro de esta etiqueta

### Stack Navigator

    npm install @react-navigation/stack@^5.x

```javascript
// In App.js in a new project

import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

function MyStack() {
  return (
    <Stack.Navigator 
        initialRouteName="Home"
        screenOptions={{  
            header: ({scene, previous, navigation}) => {
                return (<CustomHeaderBar />)
            },
            headerTintColor: 'white',
            headerStyle: { backgroundColor: 'tomato' }
            // headerMode="screen"
        }}
    >
        <Stack.Screen 
            name="Home" 
            component={Home}
            options={{
                    title: "Inicio"
            }} 
        />
        <Stack.Screen 
            name="Notifications" 
            component={Notifications}
            headerMode="modal"
            options={{headerShown: false}} 
        />
        <Stack.Screen 
            name="Profile" 
            component={Profile} 
            options={({ navigation, route }) => ({ title: route.params.name })}
        />
        <Stack.Screen name="Details" component={Details} />
        <Stack.Screen name="Settings" component={Settings} />
    </Stack.Navigator>
  );
}
```

createStackNavigator: Devuelve dos objetos, Navigator y Screen, utilizados para configurar la navegación

#### Navigator
screenOptions: Opciones compartidas entres las pantallas
- header: Función que retorna un header propio
- headerTintColor: Color del título y del ícono del botón de ir atrás
- headerStyle: Estilo de la caja (View) que contiene al título
- headerTitleStyle: Stylo del Texto del título

#### Screen
options: Opciones específicas de la pantalla (puede ser un objeto o una función)
- title: "Inicio"
- name: Nombre de la pantalla e identificador utilizado para navegar
- headerShown


#### Navegar desde una pantalla a otra

```javascript
import * as React from 'react';
import { Button, View, Text } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';

function HomeScreen({ navigation }) {  // desestructuración de parámetros
  return (
    <View>
      <Text>Home Screen</Text>
      <Button
        title="Go to Profile"
        onPress={() => navigation.navigate('Profile')} // Profile es la propiedad name especificada en la pantalla al crear el navigator
      />
    </View>
  );
}

```

Todas las pantallas reciben como propiedades el objeto *navigation* y el objeto *route*, para navegar se utilizan los siguientes métodos:
- navigation.navigate
- navigation.goBack
- navigation.push
- navigation.popToTop

#### Navegar desde una pantalla a otra pasando parámetros
1. navigation.navigate('RouteName', { /* params go here */ })

```javascript

function HomeScreen({ navigation }) {
  return (
    <View>
      <Text>Home Screen</Text>
      <Button
        title="Go to Details"
        onPress={() => {
          /* 1. Navigate to the Details route with params */
          navigation.navigate('Details', {
            itemId: 86,
            otherParam: 'anything you want here',
          });
        }}
      />
    </View>
  );
}


```

2. Para leer los parámetros consultar `route.params` en la pantalla


```javascript
function DetailsScreen({ route, navigation }) {
  /* 2. Get the param */
  const { itemId, otherParam } = route.params;
  return (
    <View>
      <Text>Details Screen</Text>

      <Text>itemId: {itemId.toString()}</Text>
      <Text>otherParam: otherParam}</Text>
    
      <Button title="Go back" onPress={() => navigation.goBack()} />
    </View>
  );
}
```

