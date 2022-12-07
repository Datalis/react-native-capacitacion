# [RXDB](https://rxdb.info/)

- Offline first ([CouchDB](https://couchdb.apache.org/), [PouchDb](https://pouchdb.com/))
- Reactiva [RxJS](https://github.com/ReactiveX/rxjs) 
- NoSql
- JS 
- Varias formas de usarla que indican dónde se van a almacenar los documentos
- Basada en PouchDb

[comment]: <> (Reactiva = Me puedo suscribir a una consulta o algún estado de la base de datos)
[comment]: <> (Offline First = El usuario puede trabajar desconectado y sus datos se replicarán a algún servidor)

### Recursos a utilizar con RxDb

#### [Adaptadores] (https://pouchdb.com/adapters.html)

PouchDb no es una base de Datos independiente si no que provee un capa de abstracción sobre otras base de datos para agregarle funcionalidades compatibles con CouchDb

- [pouchdb-adapter-react-native-sqlite] (https://github.com/craftzdog/pouchdb-react-native)
- [pouchdb-adapter-http] (https://rxdb.info/pouchdb-adapter-asyncstorage) // Habilita la sincronización sobre http

#### [RxDb-utils](https://www.npmjs.com/package/rxdb-utils)
RxDb admite plugins para que los programadores puedan proveer a la biblioteca ciertas funcionalidades faltantes

##### replication 
Permite sincronizar varias colecciones a una misma base de datos, esto agrega a todos los esquemas un campo adicional 'rx_model' con el nombre de la colección

###### Métodos adicionales que agrega ala base de datos
- replicate(remote, collections, direction, options) 

  - remote: Dirección remota a la base de datos
  - collections: Arreglo con el nombre de las colecciones a replicar
  - direction: Objetos especificando la dirección de la replicación si se van a subir los documentos o  se van a descargar
  - options: Véase más abajo

### Instalación

> npm install rxdb@8.7.5 rxdb-utils@2.0.0-beta.2  rxjs@6.5.4 react-native-sqlite-2@3.0.1 pouchdb-adapter-asyncstorage@6.4.1  pouchdb-adapter-http@7.0.0  pouchdb-adapter-react-native-sqlite@2.0.0 --save 


### Imports
```javascript
import SQLite from 'react-native-sqlite-2';
import replication from 'rxdb-utils/dist/replication';
import SQLiteAdapterFactory from 'pouchdb-adapter-react-native-sqlite';
import HttpAdapter from  'pouchdb-adapter-http';
import AsyncStorageAdapter from 'pouchdb-adapter-asyncstorage';
var RxDB = require('rxdb');
```

### Crear base

#### Registrar plugins
```javascript
const SQLiteAdapter = SQLiteAdapterFactory(SQLite);
RxDB.plugin(SQLiteAdapter);
RxDB.plugin(HttpAdapter); // enable syncing over http (remote database)
RxDB.plugin(AsyncStorageAdapter);
RxDB.plugin(replication);
```

#### Crear base
```javascript
export async function createDbInstance() {
 
  console.log('Creating DB instance');
  return await RxDB.create({
    name: 'viatcsappdb', // <- nombre
    adapter: 'react-native-sqlite', // <- storage-adapter
    multiInstance: false, // <- multiInstance (optional, default: true)
    queryChangeDetection: true, // <- queryChangeDetection (optional, default: false)
    ignoreDuplicate: true,
  });
}
```

### Cómo crear colección

#### Describir esquema 

https://json-schema.org/

```javascript
const schema = {
    version: 0,
    title: 'Expense schema',
    description: 'Describe expense fields',
    type: 'object',
    properties: {
        type: {
            string: 'string'
        },
        viaje_id: {
          type: 'string',
        },
        num_documento: {
          type: 'string',
        },
        tipo_documento: {
          type: 'object',
            properties: {
              _id: {
                type: 'string',
              },
              descripcion: {
                type: 'string',
              },
              id_tipo_documento: {
                type: 'number',
              },
              ind_viaje_ext: {
                type: 'boolean',
              },
          },
        },
        fecha_emision: {
          type: 'string',
        },
        valor_total_digitado: {
          type: ['number', 'null'],
        },
        retenciones: {
          type: 'array',
          items: {
            type: 'object',
            properties: {
              id_auxiliar_cont: {
                type: 'string',
              },
              nombre_detalle_aux: {
                type: 'string',
              },
            }
          }
        },
        attachments: {
    encrypted: false,
  },
    }
}

```

#### Crear colección

```javascript
try {
  let collection = await db.collection({
        name: `document_support_${userID}`,
        schema: invoiceSchema,
  });
} catch(e) {

}

expense_collection.preInsert(function(plainData) {
        plainData.type = 'documento';
        plainData.fecha_ingreso = now();
})

```

### Hacer una consulta

Las consultas se realizan utilizando [mangoQueries](https://github.com/mongoosejs/mquery)

- find
- findOne
- findByID

```javascript
const myCollection = await db[nombre_de_la_coleccion]
  .find({
    nombre: {$eq: 'Rosa'},
  })
  .exec();
```


### Crear Documento

```javascript

let doc = {
  nombre: 'Rosa',
  apellido: 'Ocana Bermudez'
}
await collection.insert(doc)

```

### Editar Documento

```javascript

const doc = await oldExpense.atomicUpdate(oldData => ({
      ...oldData,
      ...newExpense,
      editado_por: 'app',
}));
```

### Eliminar Documento

```javascript

await dbDoc.remove()

```


### Suscribirme a cambios en la colección ($)

```javascript
let selector = {
    type: {$eq: 'documento'},
}

let query = db.['collection_name'].find(selector);

  // todo: a esto hay qeu ponerle unsuscribe
  query.$.subscribe(async _ => {
     // hacer algo cuando esta consulta cambie
  });
```



### Eliminar base de datos
```javascript
import {removeDatabase} from 'rxdb';

await db.destroy();   // instancia
await removeDatabase('viatcsappdb', 'react-native-sqlite');
```

### Sincronizar con un endPoint
```javascript
let  REPLICATION_ENDPOINT = http://myremoteendpoint/mydb/
let collections = [
  `travels_plates_cards_logs_${userID}`,
  `gastos_notificaciones_usuario_${userID}`,
  'proveedores_empresas_nomecladores',
  `document_support_${userID}`,
]
let options = {live: true, retry: true, _conflicts: true}
let direction = {pull: true, push: true}

let liveRep = db.replicate(
    REPLICATION_ENDPOINT,
    collections,
    direction,
    options, // sync-options (optional) from https://pouchdb.com/api.html#replication
);

liveRep.connect()
```

### Suscribirme a los estados de la replicación

```javascript
currentLiveReplication.replicationStates.forEach(state => {
    if (
      state.collection.name === `travels_plates_cards_logs_${userID}` ||
      state.collection.name === `gastos_notificaciones_usuario_${userID}`
    ) {
      const subs = state.docs$.subscribe(doc => {
        if (
          doc.type === 'viaje' ||
          doc.type === 'documento' ||
          doc.type === 'notificacion'
        ) {
          console.log(
            'Database.js: Resolviendo conflictos:' + state.collection.name,
          );
          resolve_conflicts(doc, state, db);
        }
      });
      subscriptions.set(state.collection.name, subs);
    }

await currentLiveReplication.close();
await currentLiveReplication.destroy();
    
```

### Attachments

```javascript

mySchema = {
    version: 0,
    type: 'object',
    properties: {
        // .
        // .
        // .
    },
    attachments: {
        encrypted: false
    }
}

// Guadar attachment
this.collection.insert({
  nombre: this.name,
  type: this.type,
  ...this.xmlFields,
  _attachments: {
    [this.name]: {
      content_type: this.mimetype,
      data: this.data,
    },
  },
});


// Leer attachment
const doc_attachments = doc.allAttachments();
  
  // ...
    mime: doc_attachments[i].type,
    data: await doc_attachments[i].getData(), 
  // ...

```
