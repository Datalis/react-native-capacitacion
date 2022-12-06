https://github.com/jquense/yup/tree/v0.32.11#mixed

### Ejemplo

```javascript
// Formik x React Native example
import React from 'react';
import { Button, TextInput, View } from 'react-native';
import { Formik } from 'formik'; 
export const MyReactNativeForm = props => (
  <Formik
    initialValues={{ email: '' }}
    onSubmit={values => console.log(values)}
  >
    {({ handleChange, handleBlur, handleSubmit, values }) => (
      <View>
        <TextInput
          onChangeText={handleChange('email')}
          onBlur={handleBlur('email')}
          value={values.email}
        />
        <Button onPress={handleSubmit} title="Submit" />
      </View>
    )}
  </Formik>
);
```

### Paso a paso

#### 1. Importar Formik

```javascript
import { Formik } from 'formik'; 
```

#### 1. Etiqueta Formik

Definir los valores iniciales de nuestro formulario


```javascript
import { Formik } from 'formik'; 

<Formik
    initialValues={{ email: '' }}
    onSubmit={values => console.log(values)}
>
    {/* Elementos de nuestro formulario  */}
</Formik>

```


#### 2. Elementos del formulario

```javascript

{({ setFieldValue, isValid, handleChange, handleBlur, handleSubmit, values, errors }) => (
       <View>
         <Input
              placeholder="Numero del Documento"
              onChangeText={value => setFieldValue('numDoc', value)}
              errorMessage={errors.numDoc}
            />
         <Button onPress={handleSubmit} title="Submit" />
       </View>
)}
```

#### 3. Yup

#### 3.1  Importar

```javascript
import { object, string, number, date } from 'yup';

o

import * as yup from 'yup';

o 

let yup = require('yup')
```


#### 3.2 Definir esquema de validación

```javascript
import { object, string, number, date } from 'yup';

let userSchema = object({
  name: string().required(),
  age: number().required().positive().integer(),
  email: string().email(),
  website: string().url().nullable(),
  createdOn: date().default(() => new Date()),
});

```

##### string

Crea un esquema de validación para objetos js de tipo `String`


```javascript

let schema = yup
                .string() // returns a validation schema
                .required(); // returns a validation schema 
await schema.isValid('hello'); // => true
```

Los esquemas de validación no son mutables

###### .required(message) 
Añade una validación a la cadena, marca el `String` como requerido

###### .length(limit, message)
Añade una validación a la cadena, comprueba que el tamaño del string sea el especificado

Otros métodos:
.min(n, message)
.max(n, message)
.matches(regex, message)
.email(message)
.url(message)


##### number

Crea un esquema de validación para objetos js de tipo `Number`

```
let schema = yup.number();

await schema.isValid(10); // => true

```
Otros métodos:
- min(number, message)
- max(number, message)
- lessThan(number, message)
- moreThan(number, message)

##### boolean

##### object
Crea un esquema de validación para objetos

```javascript
object().shape({
  num: number(),
});
// or
object({
  num: number(),
});
```

```javascript
yup.object().shape({
  name: string().required(),
  age: number().required().positive().integer(),
  email: string().email(),
  website: string().url(),
});
```

#### Métodos comunes a todos


##### .typeError(message)
Método para cambiar el mensaje de error cuando el tipo no es el correspondido

##### .nullable(message)
Marcar el campo como nullable

##### .required(message)
Marcar campo como requerido

##### .test(name, nessage, () => {true si es valido, falso en otro caso})
Validación personalizada

```javascript
let jimmySchema = string().test(
  'nombre_de_la_validacion',
  'mensaje a mostrar',
  (value, context) => value === 'jimmy', // funcion a ejecutar, retorna true si es válido o false
); 
```

#### Validar 

##### validate
```javascript
// parse and assert validity
schema.validate({ name: 'jimmy', age: 11 }).catch(function (err) {
  err.name; // => 'ValidationError'
  err.errors; // => ['Deve ser maior que 18']
});
```

##### isValid
```javascript
schema
  .isValid({
    name: 'jimmy',
    age: 24,
  })
  .then(function (valid) {
    valid; // => true
  });
```

##### Otros métodos:
- validateSync
-isValidSync
