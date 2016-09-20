# @joshforisha/cycle-firebase&ensp;[![build](https://img.shields.io/travis/joshforisha/cycle-firebase.svg?maxAge=2592000?style=flat-square)](https://travis-ci.org/joshforisha/cycle-firebase)&ensp;[![npm](https://img.shields.io/npm/v/@joshforisha/cycle-firebase.svg?maxAge=2592000?style=flat-square)](https://www.npmjs.com/package/@joshforisha/cycle-firebase)

A [Firebase v3](https://firebase.google.com/) driver for [Cycle.js](http://cycle.js.org).

## API

* [`firebaseActions`](#firebaseActions)
* [`makeFirebaseDriver`](#makeFirebaseDriver)

### <a id="firebaseActions">firebaseActions</a>

Any write effects to the connected Firebase database should be requested by calling an _action generator_&emdash;a function defined on the `firebaseActions` object&emdash;and passed to the `firebase` source.

The following action generators are defined:

* [`createUserWithEmailAndPassword`](#firebaseActions.createUserWithEmailAndPassword)
* [`push`](#firebaseActions.push)
* [`remove`](#firebaseActions.remove)
* [`set`](#firebaseActions.set)
* [`signInWithEmailAndPassword`](#firebaseActions.signInWithEmailAndPassword)
* [`signOut`](#firebaseActions.signOut)
* [`update`](#firebaseActions.update)

#### <a id="firebaseActions.createUserWithEmailAndPassword"></a> `firebaseActions.createUserWithEmailAndPassword(email, password)`

Informs the driver to call [`firebase.auth.Auth.createUserWithEmailAndPassword()`](https://firebase.google.com/docs/reference/js/firebase.auth.Auth#createUserWithEmailAndPassword). Any errors generated by this call are emitted on the [`error` stream](#error).

```js
function main () {
  ...

  return {
    firebase: xs.of(firebaseActions.createUserWithEmailAndPassword('test@example.com', 'password'))
  }
}
```

#### <a id="firebaseActions.push"></a> `firebaseActions.push(path, values)`

Informs the driver to call [`firebase.database.Reference.push()`](https://firebase.google.com/docs/reference/js/firebase.database.Reference#push). Any errors generated by this call are emitted on the [`error` stream](#error).

```js
function main () {
  ...

  return {
    firebase: xs.of(firebaseActions.push('users/test', { firstName: 'Tester', lastName: 'McTesterson' }))
  }
}
```

#### <a id="firebaseActions.remove"></a> `firebaseActions.remove(path)`

Informs the driver to call [`firebase.database.Reference.remove()`](https://firebase.google.com/docs/reference/js/firebase.database.Reference#remove). Any errors generated by this call are emitted on the [`error` stream](#error).

```js
function main () {
  ...

  return {
    firebase: xs.of(firebaseActions.remove('users/test'))
  }
}
```

#### <a id="firebaseActions.set"></a> `firebaseActions.set(path, values)`

Informs the driver to call [`firebase.database.Reference.set()`](https://firebase.google.com/docs/reference/js/firebase.database.Reference#set). Any errors generated by this call are emitted on the [`error` stream](#error).

```js
function main () {
  ...

  return {
    firebase: xs.of(firebaseActions.set('users/test', { email: 'test@example.com' }))
  }
}
```

#### <a id="firebaseActions.signInWithEmailAndPassword"></a> `firebaseActions.signInWithEmailAndPassword(email, password)`

Informs the driver to call [`firebase.auth.Auth.signInWithEmailAndPassword()`](https://firebase.google.com/docs/reference/js/firebase.auth.Auth#signInWithEmailAndPassword). Any errors generated by this call are emitted on the [`error` stream](#error).

```js
function main () {
  ...

  return {
    firebase: xs.of(firebaseActions.signInWithEmailAndPassword('test@example.com', 'password'))
  }
}
```

#### <a id="firebaseActions.signOut"></a> `firebaseActions.signOut()`

Informs the driver to call [`firebase.auth.Auth.signOut()`](https://firebase.google.com/docs/reference/js/firebase.auth.Auth#signOut). Any errors generated by this call are emitted on the [`error` stream](#error).

```js
function main () {
  ...

  return {
    firebase: xs.of(firebaseActions.signOut())
  }
}
```

#### <a id="firebaseActions.update"></a> `firebaseActions.update(path, values)`

Informs the driver to call [`firebase.database.Reference.update()`](https://firebase.google.com/docs/reference/js/firebase.database.Reference#update). Any errors generated by this call are emitted on the [`error` stream](#error).

```js
function main () {
  ...

  return {
    firebase: xs.of(firebaseActions.update('users/test', { email: 'test@example.com' }))
  }
}
```

### <a id="makeFirebaseDriver"></a> `makeFirebaseDriver(options, name)`

Initializes a connection to a Firebase database by calling [`firebase.initializeApp()`](https://firebase.google.com/docs/reference/js/firebase#.initializeApp), returning the effective Firebase source object, which contains the following:

* [`currentUser`](#currentUser)
* [`get()`](#get)
* [`error`](#error)

#### <a id="currentUser"></a> `currentUser`

A stream of the "current user" value as returned from observing [`firebase.auth.Auth.onAuthStateChanged()`](https://firebase.google.com/docs/reference/js/firebase.auth.Auth#onAuthStateChanged).

```js
function main ({ firebase }) {
  firebase.currentUser.addListener({
    next: currentUser => console.log('Currently logged in as:', currentUser),
    ...
  })

  ...
}
```

#### <a id="get"></a> `get(path)`

Returns a stream of the ref's _value_ at `path`, by utilizing [`firebase.database.Reference.on('value')`](https://firebase.google.com/docs/reference/js/firebase.database.Reference#on).

```js
function main ({ firebase }) {
  const testUser$ = firebase.get('users/test')
  testUser$.addListener({
    next: testUser => console.log('Test user value:', testUser),
    ...
  })

  ...
}
```

#### <a id="error"></a> `error`

A stream of errors generated by the defined [firebaseActions](#firebaseActions). Each error is a [`firebase.auth.Error`](https://firebase.google.com/docs/reference/js/firebase.auth.Error).

```js
function main ({ firebase }) {
  firebase.error.addListener({
    next: err => console.error('Firebase error:', err),
    ...
  })

  ...
}
```

