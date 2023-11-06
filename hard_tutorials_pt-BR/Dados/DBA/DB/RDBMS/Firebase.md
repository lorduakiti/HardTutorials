**Adicionar o Firebase a seu projeto do JavaScript**
```
npm init
npm install --save firebase
```

Maneiras para usar o módulo do Firebase no seu aplicativo:
```
// Firebase App (the core Firebase SDK) is always required and
// must be listed before other Firebase SDKs
var firebase = require("firebase/app");

// Add the Firebase products that you want to use
require("firebase/auth");
require("firebase/firestore");
```
    .. ou
```
// Firebase App (the core Firebase SDK) is always required and
// must be listed before other Firebase SDKs
import * as firebase from "firebase/app";

// Add the Firebase services that you want to use
import "firebase/auth";
import "firebase/firestore";
```

Inicialize o Firebase no seu app:
```
// TODO: Replace the following with your app's Firebase project configuration
var firebaseConfig = {
	apiKey: "api-key",
	authDomain: "project-id.firebaseapp.com",
	databaseURL: "https://project-id.firebaseio.com",
	projectId: "project-id",
	storageBucket: "project-id.appspot.com",
	messagingSenderId: "sender-id",
	appID: "app-id",
};
```

// Initialize Firebase
`firebase.initializeApp(firebaseConfig);`

**Instalar a CLI e implantar no Firebase Hosting**
```
npm install -g firebase-tools
firebase login
firebase init
```

Implante seu conteúdo e sua configuração de hospedagem no Firebase Hosting:
`firebase deploy`
    .. ou
```
firebase.json
{
	"hosting": {
		"site": "site-name>",
		"public": "public",
		
		// ...
	}
}
```

`firebase deploy --only hosting:site-name`

https://site-name.web.app
https://site-name.firebaseapp.com

Usar vários projetos do Firebase em um único app
```
// Initialize Firebase with a "default" Firebase project
var defaultProject = firebase.initializeApp(firebaseConfig);

console.log(defaultProject.name); // "[DEFAULT]"

// Option 1: Access Firebase services via the defaultProject variable
var defaultStorage = defaultProject.storage();
var defaultFirestore = defaultProject.firestore();
```
    .. ou
```
// Option 2: Access Firebase services using shorthand notation
defaultStorage = firebase.storage();
defaultFirestore = firebase.firestore();

// Initialize Firebase with a default Firebase project
firebase.initializeApp(firebaseConfig);

// Initialize Firebase with a second Firebase project
var otherProject = firebase.initializeApp(otherProjectFirebaseConfig, "other");

console.log(firebase.app().name); // "[DEFAULT]"
console.log(otherProject.name); // "otherProject"

// Use the shorthand notation to access the default project's Firebase services
var defaultStorage = firebase.storage();
var defaultFirestore = firebase.firestore();

// Use the otherProject variable to access the second project's Firebase services
var otherStorage = otherProject.storage();
var otherFirestore = otherProject.firestore();
```

**Executar um servidor da Web local para desenvolvimento**
```
npm install -g firebase-tools
firebase login
firebase init
firebase serve
```