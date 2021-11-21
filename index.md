## Mi servidor NODE ❤️

Pruebo mi servidor con:
~~~
npm run devbabel
~~~

### Crear mi servidor node

Seguir pasos: https://bluuweb.github.io/mevn/01-primeros-pasos/#crear-servidor

* Crear una carpeta en alguna parte de tu computador
* Arrastrar dicha carpeta a Visual Studio Code
* Ejecutar un nuevo proyecto de Node.js
~~~
npm init --yes
~~~


### Instalar Express.js 
~~~
npm install express --save
~~~
#### Crear archivo app.js
~~~
const express = require('express');
const app = express();

app.get('/', function (req, res) {
  res.send('Hello World!');
});

app.listen(3000, function () {
  console.log('Example app listening on port 3000!');
});
~~~
La aplicación inicia un servidor y escucha las conexiones en el puerto 3000. La aplicación responde con “Hello World!” para las solicitudes al URL raíz (/) o a la ruta raíz. Para cada vía de acceso diferente, responderá con un error 404 Not Found.

A continuación, cargue http://localhost:3000/ en un navegador para ver la salida.
//Para que detecte los cambios nuestro servidor podemos instalar https://www.npmjs.com/package/nodemon
~~~
npm install -g nodemon
~~~
Configurar nuevo script en el apartado "scripts" en package.json
~~~
"dev": "nodemon app.js"
~~~
Ahora ejecutar:
~~~
npm run dev
~~~
Asignar puerto automáticamente:
~~~
app.set('puerto', process.env.PORT || 3000);
app.listen(app.get('puerto'), function () {
  console.log('Example app listening on port'+ app.get('puerto'));
});
~~~
#### Instalo morgan para ver por terminal las peticiones que se hacen (que alguien ha accedido): 
~~~
npm i morgan --save
~~~
En mi app.js incluyo:
~~~
const morgan = require('morgan');
~~~
Con lo siguiente express utiliza morgan
~~~
app.use(morgan('tiny'));
~~~
#### Instalar CORS
~~~
npm install cors --save
~~~
Enable All CORS Requests
~~~
const cors = require('cors');
app.use(cors());
~~~
#### Instalar JSON y urlencoded (porque vamos a usar JSONS)
Espress ya tiene instalado esto por eso sólo lo configuramos:
~~~
app.use(express.json());

//application/x-www-form-urlencoded
app.use(express.urlencoded({ extended: true }))
~~~

Ahora vamos a configurar una ruta estática porque lo que ahora es hola mundo, después será todo el vue

#### Creo la carpeta public
* Creo una carpeta llamada public al mismo nivel que app.js (es donde irá toda mi aplicación de view)

* En app.js pondremos para indicarle que utilize la ruta actual (ahora es local, pero después será el servidor)

* Para acceder al directorio actual
const path = require('path');

#### History Vue.js
Vue.js utiliza el modo History para simular las rutas de un sitio web, ya que al ser SPA es un simple HTML, esto nos puede traer problemas con Express por lo tanto agregaremos otro Middleware de configuración.
~~~
npm install --save connect-history-api-fallback
~~~
Y ahora pondremos lo siguiente en app.js (justo antes de: app.use(express.static(path.join(__dirname, 'public')));)IMPORTANTE

#### Middleware para Vue.js router modo history
~~~
const history = require('connect-history-api-fallback');
app.use(history());
~~~
Ahora se reordena para que esté justo debajo de app.get('/', function (req, res) { res.send('Hello World!');
});

#### En resumen nuestro archivo app.js debería ir quedando así:
~~~
const express = require('express');
const morgan = require('morgan');
const cors = require('cors');
const path = require('path');

const app = express();

// Middleware
app.use(morgan('tiny'));
app.use(cors());
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
//app.use(express.static(path.join(__dirname, 'public')));

// Rutas
app.get('/', function (req, res) {
  res.send('Hello World!');
});

// Middleware para Vue.js router modo history
const history = require('connect-history-api-fallback');
app.use(history());
app.use(express.static(path.join(__dirname, 'public')));

app.set('puerto', process.env.PORT || 3000);
app.listen(app.get('puerto'), function () {
  console.log('Example app listening on port'+ app.get('puerto'));
});
~~~
#### Instalamos babel para trabajar con las últimas versiones de json

~~~
npm install -D @babel/core @babel/cli @babel/preset-env @babel/node
~~~
Crear archivo .babelrc en la raíz del sistema con:
~~~
{
  "presets": ["@babel/preset-env"]
}
~~~

y en package.js, y reemplazo el objeto de script por esto:
~~~
"scripts": {
  "dev": "nodemon app.js",
  "devbabel": "nodemon app.js --exec babel-node"
},
~~~
Una vez hecho esto, podemos ELIMINARLO TODO Y PONERLO EN JS a ES6:
~~~
import express from 'express';
import morgan from 'morgan';
import cors from 'cors';
import path from 'path';

const app = express();

// Middleware
app.use(morgan('tiny'));
app.use(cors());
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
// app.use(express.static(path.join(__dirname, 'public')));

// Rutas
app.get('/', (req, res) => {
  res.send('Hello World!');
});

// Middleware para Vue.js router modo history
const history = require('connect-history-api-fallback');
app.use(history());
app.use(express.static(path.join(__dirname, 'public')));

app.set('puerto', process.env.PORT || 3000);
app.listen(app.get('puerto'), () => {
  console.log('Example app listening on port'+ app.get('puerto'));
});
~~~

#### Pruebo mi servidor con:
~~~
npm run devbabel
~~~
## Ahora lo pongo todo en un servidor gratis!!! que permite trabajar con nodejs

https://id.heroku.com/login

* Una vez ahí creo mi app y me lleva a "deploy donde le digo deployment method Heroku git"

* Sólo la primera vez instalar heroku cli:

https://devcenter.heroku.com/articles/heroku-cli

* Ahora hago:
~~~
$ heroku login
~~~
* Entonces me lleva a login
* vamos a nuestro proyecto de back
* Creamos archivo .gitignore
* incluimos en el archivo:
~~~
node_modules
~~~
* iniciamos git con:
~~~
git init
~~~
y también ponemos en terminal:
~~~
heroku git:remote -a mi-primer-node
~~~
* incluimos en el package.json, en scripts (ponerlo el primero de la ista de scripts):
~~~
"start": "babel-node app.js --exec",
~~~
en el mismo package.json, copio todo lo que hay dentro de devdependencias (desarrollo) y lo pego en dependencias(produccion):
~~~
"@babel/cli": "^7.14.5",
    "@babel/core": "^7.14.6",
    "@babel/node": "^7.14.5",
    "@babel/preset-env": "^7.14.5"
~~~
* hago un git add (o lo hago desde vs) y hago commit

* ahora hago:
~~~
git push heroku master
~~~
* Vamos a las bases de datos:

### Instalamos mongoose que nos ayuda:
~~~
npm install mongoose --save
~~~
* En nuestro app.js configurar https://mongoosejs.com/docs/connections.html#callback 

y escogemos el de promesa que pondremos en app.js junto con la importanción de mongoose:

~~~
// DB Conection
import mongoose from 'mongoose';

mongoose.connect(uri, options).then(
  () => { console.log('Conectado a MongoDB')},
  err => { err}
);
~~~
* AHORA justo debajo DE import mongoose from 'mongoose' creamos la const uri (será igual a la url de nuestra base de datos):
~~~
const uri = 'mongodb://localhost:27017/<nombre base de datos>'; //de momento pongo mi máquina
~~~

#### Mi database
(Ver tutorial aquí https://www.youtube.com/watch?v=IpQSsb-1N1g&list=PLPl81lqbj-4IEnmCXEJeEXPepr8gWtsl6&index=13&ab_channel=Bluuweb%21Bluuweb%21Verificada)

* Ir a: https://bluuweb.github.io/node/05-db/#:~:text=https%3A//cloud.mongodb.com/

* Para entrar donde están mis database ir a Browser collections

* Una vez creado el cluster voy al botón "collections:

* Le damos a "create database"

* DataBase name: pe, veterinaria
* Collection name: pe, mascotas

* Le damos a crear

* Hago click sobre el título y le doy a "insertar documento"

* Creamos usuario y password en security/Database access

Y colocamos en app.js encima de const Uri:
~~~
const user= '';
const password= '';
~~~
El uri lo obtengo en Cluster, botón connect, conect your aplicación y cojo esto (o parecido):
mongodb+srv://minode-9090:<password>@cluster0.ap7e3.mongodb.net/myFirstDatabase?retryWrites=true&w=majority

Y lo pego en mi app.js:
~~~
const uri = `mongodb+srv://minode-9090:<password>@cluster0.ap7e3.mongodb.net/myFirstDatabase?retryWrites=true&w=majority`;
~~~~~~
Quedará así:
~~~
// DB Conection
import mongoose from 'mongoose';

const user = 'minode-9090';
const password = 'MG20070831';
const uri = `mongodb+srv://${user}:${password}@cluster0.ap7e3.mongodb.net/prueba?retryWrites=true&w=majority`;
const options = { useNewUrlParser: true, useUnifiedTopology: true };
mongoose.connect(uri, options).then(
  () => { console.log('Conectado a MongoDB')},
  err => { console.log(err) }
);
// Estoy conectado a mongo?? 0: disconnected, 1: connected, 2: connecting, 3: disconnecting
console.log(mongoose.connection.readyState);
~~~

__________________________

### Schemas

En la raiz Creo la carpeta "models"

Y dentro de ella un js con el nombre de lo que contenga nuestra database, pe, nota.js y en ella pongo algo así:
~~~
import mongoose from 'mongoose';
const Schema = mongoose.Schema;

const notaSchema = new Schema({
//Nombre es obligatorio y saltará el error Nombre obligatorio
  nombre: {type: String, required: [true, 'Nombre obligatorio']},
  descripcion: String,
  usuarioId: String,
  date:{type: Date, default: Date.now},
  activo: {type: Boolean, default: true}
});
// Convertir a modelo
const Nota = mongoose.model('Nota', notaSchema);

export default Nota;
~~~

Ahora creo las rutas:

- Rutas (POST)

Creo una carpeta en raiz llamada "routes" y creo un js con el mismo nombre que llamé al modelo y pongo esto:
~~~
import express from 'express';
const router = express.Router();

// importar el modelo nota
import Nota from '../models/nota';

// Agregar una nota
router.post('/nueva-nota', async(req, res) => {
  const body = req.body;  
  try {
    const notaDB = await Nota.create(body);
    res.status(200).json(notaDB); 
  } catch (error) {
    return res.status(500).json({
      mensaje: 'Ocurrio un error',
      error
    })
  }
});

// Exportamos la configuración de express app
module.exports = router;
~~~
Me voy a mi app.js y coloco en las rutas, para llamar a la ruta que he creado
~~~
app.use('/api', require('./routes/nota'))

// Get con parámetros
router.get('/nota/:id', async(req, res) => {
  const _id = req.params.id;
  try {
    const notaDB = await Nota.findOne({_id});
    res.render("mascotas", {
            listaMascotas: "Aquí irán todas las mascotas",
            arrayMascotas
   })
  } catch (error) {
    return res.status(400).json({
      mensaje: 'Ocurrio un error',
      error
    })
  }
});

// Get con todas las Recetas (en este caso)
router.get('/recetas', async(req, res) => {
    try {
      const recetaDb = await Receta.find();
      res.json(recetaDb);
    } catch (error) {
      return res.status(400).json({
        mensaje: 'Ocurrio un error',
        error
      })
    }
  });
// Delete eliminar una nota
router.delete('/nota/:id', async(req, res) => {
  const _id = req.params.id;
  try {
    const notaDb = await Nota.findByIdAndDelete({_id});
    if(!notaDb){
      return res.status(400).json({
        mensaje: 'No se encontró el id indicado',
        error
      })
    }
    res.json(notaDb);  
  } catch (error) {
    return res.status(400).json({
      mensaje: 'Ocurrio un error',
      error
    })
  }
});

// Put actualizar una nota
router.put('/nota/:id', async(req, res) => {
  const _id = req.params.id;
  const body = req.body;
  try {
    const notaDb = await Nota.findByIdAndUpdate(
      _id,
      body,
      {new: true});
    res.json(notaDb);  
  } catch (error) {
    return res.status(400).json({
      mensaje: 'Ocurrio un error',
      error
    })
  }
});
~~~
### USO VARIABLES DE ENTORNO:https://bluuweb.github.io/node/05-db/#varibles-de-entorno

Instalo: 
 ~~~
 npm i dotenv
~~~
https://www.npmjs.com/package/dotenv

En el app.js debería poner después de importar express:
~~~
require('dotenv').config()
~~~
Creo en la raíz el directorio .env

Y pongo: 
~~~
PORT=3000 (el puerto que haya puesto)
USUARIO=xxx
PASSWORD=xxx
DBNAME=xxx
~~~
Depues hago los cambios en app.js
~~~
const uri = `mongodb+srv://${process.env.USUARIO}:${process.env.PASSWORD}@cluster0.ncdk5.mongodb.net/${process.env.DBNAME}?retryWrites=true&w=majority`;
~~~
e INCLUYO EL .ENV EN GITINGNORE
~~~
node_modules
.env
~~~
Puedes poner las variables de entorno en el servidor heroku: https://devcenter.heroku.com/articles/config-vars

iNSTALAMOS para utilizar x-www-form-urlencoded:
 ~~~
npm i body-parser
~~~

Añado en app.js:
~~~
import bodyParser from 'body-parser';

// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))
// parse application/json
app.use(bodyParser.json())
~~~
Recordar!!! Comentar las rutas de hello world de node y poner en su lugar: 
~~~
app.use('/api', require('./routes/nota'))
~~~
### Autenticación:

Creo un nuevo modelo llamado user (en models, donde ja está el de receta)

Importo mongoose y el esquema:
~~~
import mongoose from 'mongoose';
const Schema = mongoose.Schema;
~~~
__________

Y creo la constante del esquema de user:
~~~
const userSchema = new Schema({
//Nombre es obligatorio y saltará el error Nombre obligatorio
  nombre: {type: String, required: [true, 'Nombre obligatorio']},
  email: { type: String, unique: true, required: [true, 'Email es necesario'] },
  pass: { type: String, required: [true, 'Pass es necesario'] },
  date: { type: Date, default: Date.now },
  // Los roles los vamos a colocar encima de const userSchema
  role: { type: String, default: 'USER', enum: roles },
  activo: { type: Boolean, default: true }
});
~~~
#### De tal forma que todo el arx. Quedará así:
~~~
import mongoose from 'mongoose';
const Schema = mongoose.Schema;

// Roles
const roles = {
    values: ['ADMIN', 'USER'],
    message: '{VALUE} no es un rol válido'
}

const userSchema = new Schema({
//Nombre es obligatorio y saltará el error Nombre obligatorio
  nombre: {type: String, required: [true, 'Nombre obligatorio']},
  email: { type: String, unique: true, required: [true, 'Email es necesario'] },
  pass: { type: String, required: [true, 'Pass es necesario'] },
  date: { type: Date, default: Date.now },
  role: { type: String, default: 'USER', enum: roles },
  activo: { type: Boolean, default: true }
});

// Convertir a modelo
const User = mongoose.model('User', userSchema);

module.exports = User;
~~~
_______

Ahora creo la ruta de user: llamado user.js

Y ahí importo:
~~~
import express from 'express';
const router = express.Router();

// importar el modelo user
import User from '../models/user';


// Agregar un nuevo user
router.post('/nuevo-usuario', async(req, res) => {
    const body = req.body;  
    try {
      const usuarioDB = await User.create(body);
      res.status(200).json(usuarioDB); 
    } catch (error) {
      return res.status(500).json({
        mensaje: 'Ocurrió un error',
        error
      })
    }
  });
Y poner el export:

module.exports = router;
~~~

Este router que exportamos lo tenemos que utilizar en el app.js. En el app.js buscamos la línea donde hacíamos esto:
~~~
app.use('/api', require('./routes/receta')) 
~~~
Y la duplicamos justo debajo y reemplazamos receta por user:
~~~
app.use('/api', require('./routes/user'))
~~~
Ahora vamos a instalar un validar único de mongoose para que el email sea único:

https://www.npmjs.com/package/mongoose-unique-validator
~~~
npm i mongoose-unique-validator --save
~~~
Me vuelvo a el modelo user.js y debajo de const Schema pongo:
~~~
const uniqueValidator = require('mongoose-unique-validator');
~~~
Uso esta constante debajo del const userSchema:
~~~
// Validator
userSchema.plugin(uniqueValidator, { message: 'Error, esperaba {PATH} único.' });
~~~
Entonces me voy al email y le digo que tiene que ser único: debe quedar así:
~~~
email: { type: String, unique: true, required: [true, 'Email es necesario'] },
~~~

Ahora vamos a encripatr la contraseña:

En user.js de ROUTES:

mIRO DEONDE ESTÁ "const body = req.body;"
~~~
// Agregar un nuevo user
router.post('/nuevo-usuario', async(req, res) => {
    const body = req.body;  
    try {
      const usuarioDB = await User.create(body);
      res.status(200).json(usuarioDB); 
    } catch (error) {
      return res.status(500).json({
        mensaje: 'Ocurrió un error',
        error
      })
    }
  });
~~~
Ahora lo reemplazo por: 

~~~
const body = {
    nombre: req.body.nombre,
    email: req.body.email,
    role: req.body.role
  }

  body.pass = bcrypt.hashSync(req.body.pass, saltRounds);
~~~
TENEMOS QUE INSTALAR BCRYPT: https://www.npmjs.com/package/bcrypt
~~~
npm i bcrypt
~~~
Ahora ponemos debajo de los imports:
~~~
// Hash Contraseña
const bcrypt = require('bcrypt');
const saltRounds = 10;
~~~
Ahora tenemos que esconder la contraseña cuando la traigamos con un json, de esta forma:

Nos vamos al schema en models user.js

Y ponemos justo debajo de: userSchema.plugin(uniqueValidator, { message: 'Error, esperaba {PATH} único.' });


Esto:
~~~
// Eliminar pass de respuesta JSON
userSchema.methods.toJSON = function() {
  var obj = this.toObject();
  delete obj.pass;
  return obj;
 }
~~~
Hacemos el put de user en la router del user:

Y en el: {new: true}); añadimos la validación de usuario

De manera que el put en el router usuario quedará así:
~~~
// Put actualizar una receta
  router.put('/user/:id', async(req, res) => {
    const _id = req.params.id;
    const body = req.body;
    try {
      const usuarioDB = await User.findByIdAndUpdate(id, body, {new: true, runValidators: true});
      res.json(usuarioDB);  
    } catch (error) {
      return res.status(400).json({
        mensaje: 'Ocurrió un error',
        error
      })
    }
  });
~~~
Ahora nos encontramos con que la contraseña viene sin encintar y que hay campos de user que no queremos que el usuario pueda editar, para evitar todo esto nos apoyamos en underscore que nos permitirá agregar validaciones:
~~~
npm install underscore --save
~~~

Después del hash de contraseña pondremos:
~~~
// Filtrar campos de PUT
const _ = require('underscore');
~~~
Cambiamos esto del put:
 ~~~
 const body = req.body;
~~~
Por: 
~~~
let body = _.pick(req.body, ['nombre', 'email', 'activo', 'pass']);
~~~
Es decir le indicamos los campos que puede cambiar el usuario

Justo debajo de esta línea que acabamos de cambiar y antes del try ponemos:
~~~
//Si modifico la contraseña, hay que encriptarla
    if(body.pass){
      body.pass = bcrypt.hashSync(req.body.pass, saltRounds);
    }
~~~
Ahora vamos a hacer en node la router del login (esquema (model) no):

Importo:
~~~
const express = require('express');
const router = express.Router();
~~~
A continuación en user: import User from '../models/user';

Seguidamente:

//Habrá que desincriptar la password:
~~~
const bcrypt = require('bcrypt');
const saltRounds = 10;
~~~
Termino el documento con:
~~~
//Uso 'post' para que la info viaje oculta
router.post('/', async(req, res) => {

    let body = req.body;
  
    try {
      // Buscamos email en DB
      const usuarioDB = await User.findOne({email: body.email});
  
      // Evaluamos si existe el usuario en DB
      if(!usuarioDB){
        return res.status(400).json({
          mensaje: 'Usuario! o contraseña inválidos',
        });
      }
  
      // Evaluamos la contraseña correcta
      if( !bcrypt.compareSync(body.pass, usuarioDB.pass) ){
        return res.status(400).json({
          mensaje: 'Usuario o contraseña! inválidos',
        });
      }
  
      // Pasó las validaciones
      return res.json({
        usuarioDB,
        token: 'fkajsdkf'
      })
      
    } catch (error) {
      return res.status(400).json({
        mensaje: 'Ocurrio un error',
        error
      });
    }
  
  });
  
module.exports = router;
~~~

Me voy a app.js y debajo de esto:
~~~
app.use('/api', require('./routes/receta'))
app.use('/api', require('./routes/user'))
~~~
Pongo esto:
~~~
app.use('/login', require('./routes/login'));
~~~
-----
### JWT (Jason web token)

Usaremos esto para generar tokens (más seguridad):
~~~
npm i jsonwebtoken --save
~~~

Me voy a mi login.js y pongo antes del import user:
~~~
// JWT
const jwt = require('jsonwebtoken');
~~~
Entonces, debajo del código de //evaluamos contraseña pongo:
~~~
// Generar Token
    let token = jwt.sign({
        data: usuarioDB
    }, 'secret', { expiresIn: 60 * 60 * 24 * 30}) // Expira en 30 días
~~~
Y en els siguiente trozo de código, pongo el token, es decir cambio esto:

token: 'fkajsdkf'

Por esto:
~~~
token: token

~~~
Continuará...

## Mi APP VUE ❤️
~~~
yarn serve / npm run serve 
~~~
https://bluuweb.github.io/mevn/03-vue/

Herramientas estándar para el desarrollo de Vue.js: VUE CLI
vuecli: https://cli.vuejs.org/ (es de forma global en todo el equipo):
~~~
yarn global add @vue/cli
~~~
creo carpeta del proyecto y la abro en consola y pongo:
 ~~~
vue create <nombre>
~~~
Añadir bootstrap a vue:
Bootstrap-vue:
~~~
  # With npm
npm install vue bootstrap bootstrap-vue

# With yarn
yarn add vue bootstrap bootstrap-vue
~~~
En main: 
~~~
import { BootstrapVue, IconsPlugin } from 'bootstrap-vue'

// Import Bootstrap an BootstrapVue CSS files (order is important)
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

// Make BootstrapVue available throughout your project
Vue.use(BootstrapVue)
// Optionally install the BootstrapVue icon components plugin
Vue.use(IconsPlugin)
~~~
Instalamos Axios
~~~
npm install --save axios vue-axios
~~~

Esto en el main para hacer las llamadas a la api
~~~
import axios from 'axios'
import VueAxios from 'vue-axios'
 
Vue.use(VueAxios, axios)
~~~
Despues de use.axios poner:
~~~
// Agregamos la URL base de nuestra API
axios.defaults.baseURL = 'https://mi-node-2.herokuapp.com/api';
~~~
// Si trabajamos en local
axios.defaults.baseURL = 'http://localhost:3000/api';
~~~

Hago en el vue:
~~~
yarn serve
~~~

tener activa la base de datos en https://cloud.mongodb.com/

Y ejecuto node también 
~~~
npm run devbabel
~~~

### SI FALLA MONGO, Mira que ip tienes y la colocas en security/network

NOTA: Al usar axios-vue de que puedo usar el this.axios.get… en cualquier médotodo

Al no usar vuex, hago las llamadas a la api desde el componente en create() rellenando un array de notas, en este caso que estará en data:

~~~
<script>
export default {
  name: 'Notas',
  data() {
    return {
      notas: [],
    };
  },
  created() {
    this.listarNotas();
  },
  methods: {
    listarNotas() {
      this.axios.get('/nota')
        .then((res) => {
          console.log(res);
        })
        .catch((e) => {
          console.log(e.response);
        });
    },
  },
};
</script>

~~~

A qué ruta llamamos con axios? A la que está configurada en main.js, pero ya está automática así que solo tengo que poner el ‘/nota’, porque nota? Porque en mi backend en rutas lo configuré así:
~~~
// Get con todas las notas
router.get('/nota', async(req, res) => {
  try {
    const notaDb = await Nota.find();
    res.json(notaDb);
  } catch (error) {
    return res.status(400).json({
      mensaje: 'Ocurrio un error',
      error
    })
  }
});
~~~
Si te da error, primero mira que tengas configurada tu ip en mongo db en security/network access y si sigue fallando comprueba que el puerto que tienes en el backend, en la variable de entorno es la misma que la que tienes en front, en main.js: hacemos

rECUERDA PONR EN LA RUTA LA NUEVA VISTA CREADA:
~~~
app.use('/api', require('./routes/receta'))
~~~

Abrimos consola de nuestra aplicación (ahora es el que era el backend, recuerda! Y ponemos:
~~~
heroku login
~~~
git init
git add.
gir commit -m””
~~~
git push heroku master
~~~
Y ponemos la dirección que esté en la web de heroku ir a nuestra app en el main.js de que, en los archivos de que, no backend:

Para pasar a producción hago:
~~~
npm run-script build
~~~
en mi front y después todo lo que hay DENTRO de la nueva carpeta “dis” en la carpeta public de mi backend, tengo que eliminar el index.html que había puesto en él.

Asegurarse que el servidor estaba iniciado y si ahora voy a mi navegador y pongo: localhost:3001 (o el puerto en el que tenía el backend) veré mi aplicación. 

Comprobamos el funcionamiento de la aplicación para verificar que está todo correcto.

Abrimos consola de nuestra aplicación (ahora es el que era el backend, recuerda! Y ponemos:
~~~
heroku login
~~~
~~~
git init
git add.
gir commit -m””
~~~
~~~
git push heroku master
~~~

STORE REVISAR TRABAJO DE: 

https://github.com/carlosazaustre/vue-trello/tree/master/src/store

https://github.com/bradtraversy/vuex_todo_manager/tree/master/src



