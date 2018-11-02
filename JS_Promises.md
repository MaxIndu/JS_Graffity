# JS Promises

Ref - https://www.taniarascia.com/promise-all-with-async-await/

# Promise.all with Async/Await
##### October 4, 2018  /  3 responses	

Let’s say I have an API call that returns all the users from a database and takes some amount of time to complete.

```javascript
// First promise returns an array after a delay
const getUsers = () => {
  return new Promise((resolve, reject) => {
    return setTimeout(
      () => resolve([
        { id: 'jon' }, 
        { id: 'andrey' }, 
        { id: 'tania' }]),
      600
    )
  })
}
```

Now there’s another call that relies on some information that exists in the entire array of users.

```javascript
// Second promise relies on the result of first promise
const getIdFromUser = users => {
  return new Promise((resolve, reject) => {
    return setTimeout(() => resolve(users.id), 500)
  })
}
```

And a third call that modifies the second call.

```javascript
// Third promise relies on the result of the second promise
const capitalizeIds = id => {
  return new Promise((resolve, reject) => {
    return setTimeout(() => resolve(id.toUpperCase()), 200)
  })
}
```

I might try to run the first call, then use a for...of loop to run the subsequent calls that rely on it.

```javascript
const runAsyncFunctions = async () => {
  const users = await getUsers()

  for (let user of users) {
    const userId = await getIdFromUser(user)
    console.log(userId)

    const capitalizedId = await capitalizeIds(userId)
    console.log(capitalizedId)
  } 

  console.log(users)
}

runAsyncFunctions()
```

However, this will be my output:

```
jon
JON
andrey
ANDREY
tania
TANIA
(3) [{…}, {…}, {…}]
```

I can use Promise.all() instead to run all of the first, then all the second, then all the third functions.

```javascript
const runAsyncFunctions = async () => {
  const users = await getUsers()

  Promise.all(
    users.map(async user => {
      const userId = await getIdFromUser(user)
      console.log(userId)

      const capitalizedId = await capitalizeIds(userId)
      console.log(capitalizedId)
    })
  )

  console.log(users)
}
```

```
(3) [{…}, {…}, {…}]
jon
andrey
tania
JON
ANDREY
TANIA
```
Hope that helps someone.

Here’s the whole snippet you can run in the console.

```javascript
// First promise returns an array 
const getUsers = () => {
  return new Promise((resolve, reject) => {
    return setTimeout(
      () => resolve([
        { id: 'jon' }, 
        { id: 'andrey' }, 
        { id: 'tania' }]),
      600
    )
  })
}

// Second promise relies on the resulting array of first promise
const getIdFromUser = users => {
  return new Promise((resolve, reject) => {
    return setTimeout(() => resolve(users.id), 500)
  })
}

// Third promise relies on the result of the second promise
const capitalizeIds = id => {
  return new Promise((resolve, reject) => {
    return setTimeout(() => resolve(id.toUpperCase()), 200)
  })
}

const runAsyncFunctions = async () => {
  const users = await getUsers()

  Promise.all(
    users.map(async user => {
      const userId = await getIdFromUser(user)
      console.log(userId)

      const capitalizedId = await capitalizeIds(userId)
      console.log(capitalizedId)
    })
  )

  console.log(users)
}

runAsyncFunctions()
```