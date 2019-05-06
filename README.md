```
                  .x+=:.                  .....                          s                          
                 z`    ^%              .H8888888x.  '`+                 :8                .uef^"    
   x.    .          .   <k            :888888888888x.  !               .88              :d88E       
 .@88k  z88u      .@8Ned8"      .u    8~    `"*88888888"      .u      :888ooo       .   `888E       
~"8888 ^8888    .@^%8888"    ud8888.  !      .  `f""""     ud8888.  -*8888888  .udR88N   888E .z8k  
  8888  888R   x88:  `)8b. :888'8888.  ~:...-` :8L <)88: :888'8888.   8888    <888'888k  888E~?888L 
  8888  888R   8888N=*8888 d888 '88%"     .   :888:>X88! d888 '88%"   8888    9888 'Y"   888E  888E 
  8888  888R    %8"    R88 8888.+"     :~"88x 48888X ^`  8888.+"      8888    9888       888E  888E 
  8888 ,888B .   @8Wou 9%  8888L      <  :888k'88888X    8888L       .8888Lu= 9888       888E  888E 
 "8888Y 8888"  .888888P`   '8888c. .+   d8888f '88888X   '8888c. .+  ^%888*   ?8888u../  888E  888E 
  `Y"   'YP    `   ^"F      "88888%    :8888!    ?8888>   "88888%      'Y"     "8888P'  m888N= 888> 
                              "YP'     X888!      8888~     "YP'                 "P'     `Y"   888  
                                       '888       X88f                                        J88"  
                                        '%8:     .8*"                                         @%    
                                           ^----~"`                                         :"      
```
<h1 align="center">useFetch</h1>
<p align="center">🐶 React hook for making isomorphic http requests</p>
<p align="center">
    <a href="https://github.com/alex-cory/use-http/pulls">
      <img src="https://camo.githubusercontent.com/d4e0f63e9613ee474a7dfdc23c240b9795712c96/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5052732d77656c636f6d652d627269676874677265656e2e737667" />
    </a>
    <a href="https://circleci.com/gh/alex-cory/use-http">
      <img src="https://img.shields.io/circleci/project/github/alex-cory/use-http/master.svg" />
    </a>
    <a href="https://www.npmjs.com/package/use-http">
      <img src="https://img.shields.io/npm/dt/use-http.svg" />
    </a>
    <br />
    <a href="https://lgtm.com/projects/g/alex-cory/use-http/context:javascript">
      <img alt="undefined" src="https://img.shields.io/lgtm/grade/javascript/g/alex-cory/use-http.svg?logo=lgtm&logoWidth=18"/>
    </a>
    <a href="https://github.com/alex-cory/use-http/releases">
      <img alt="undefined" src="https://img.shields.io/github/release/alex-cory/use-http.svg?style=popout">
    </a>
</p>

<img align="right" src="https://media.giphy.com/media/fAFg3xESCJyw/giphy.gif" />
Need to fetch some data? Try this one out. It's an isomorphic fetch hook. That means it works with SSR (server side rendering).

### Examples
- <a target="_blank" rel="noopener noreferrer" href='https://codesandbox.io/embed/km04k9k9x5'>Code Sandbox Example</a>

Installation
------------

```shell
yarn add use-http    or    npm i -S use-http
```

Usage
-----
#### Basic Usage

```jsx 
import useFetch from 'use-http'

function Todos() {
  const options = { // accepts all `fetch` options
    onMount: true // will fire on componentDidMount
  }
  
  const todos = useFetch('https://example.com/todos', options)
  
  const addTodo = () => {
    todos.post({
      title: 'no way',
    })
  }

  if (todos.error) return 'Error!'
  if (todos.loading) return 'Loading...'
  
  return (
    <>
      <button onClick={addTodo}>Add Todo</button>
      {todos.data.map(todo => (
        <div key={todo.id}>{todo.title}</div>
      )}
    </>
  )
}
```
#### Destructured methods
```jsx
var [data, loading, error, request] = useFetch('https://example.com')

// want to use object destructuring? You can do that too
var { data, loading, error, request } = useFetch('https://example.com')

request.post({
  no: 'way',
})
```
#### Relative routes
```jsx
const request = useFetch({
  baseUrl: 'https://example.com'
})

request.post('/todos', {
  no: 'way'
})
```
#### Helper hooks

```jsx
import { useGet, usePost, usePatch, usePut, useDelete } from 'use-http'

const [data, loading, error, patch] = usePatch({
  url: 'https://example.com',
  headers: {
    'Content-type': 'application/json; charset=UTF-8'
  }
})

patch({
  yes: 'way',
})
```

#### Abort

<img src="public/abort-example-1.gif" height="250" />


```jsx
const githubRepos = useFetch({
  baseUrl: `https://api.github.com/search/repositories?q=`
})

// the line below is not isomorphic, but for simplicity we're using the browsers `encodeURI`
const searchGithubRepos = e => githubRepos.get(encodeURI(e.target.value))

<>
  <input onChange={searchGithubRepos} />
  <button onClick={githubRepos.abort}>Abort</button>
  {githubRepos.loading ? 'Loading...' : githubRepos.data.items.map(repo => (
    <div key={repo.id}>{repo.name}</div>
  ))}
</>
```

#### The Goal With Suspense <sup><strong>(not implemented yet)</strong></sup>
```jsx
import React, { Suspense, unstable_ConcurrentMode as ConcurrentMode, useEffect } from 'react'

const WithSuspense = () => {
  const suspense = useFetch('https://example.com')

  useEffect(() => {
    suspense.read()
  }, [])

  if (!suspense.data) return null

  return <pre>{suspense.data}</pre>
}

const App = () => (
  <ConcurrentMode>
    <Suspense fallback="Loading...">
      <WithSuspense />
    </Suspense>
  </ConcurrentMode>
)
```

Hooks
----
| Option                | Description                                                                              |
| --------------------- | ---------------------------------------------------------------------------------------- |
| `useFetch` | The base hook |
| `useGet` | Defaults to a GET request |
| `usePost` | Defaults to a POST request |
| `usePut` | Defaults to a PUT request |
| `usePatch` | Defaults to a PATCH request |
| `useDelete` | Defaults to a DELETE request |

Options
-----

This is exactly what you would pass to the normal js `fetch`, with a little extra.

| Option                | Description                                                               |  Default     |
| --------------------- | --------------------------------------------------------------------------|------------- |
| `onMount` | Once the component mounts, the http request will run immediately | false |
| `baseUrl` | Allows you to set a base path so relative paths can be used for each request :)       | empty string |

```jsx
const {
  data,
  loading,
  error,
  request,
  get,
  post,
  patch,
  put,
  delete // don't destructure `delete` though, it's a keyword
  del,   // <- that's why we have this (del). or use `request.delete`
  abort,
} = useFetch({
  url: 'https://example.com',
  baseUrl: 'https://example.com',
  onMount: true
})
```
or
```jsx
const [data, loading, error, request] = useFetch({
  url: 'https://example.com',
  baseUrl: 'https://example.com',
  onMount: true
})

const {
  get,
  post,
  patch,
  put,
  delete // don't destructure `delete` though, it's a keyword
  del,   // <- that's why we have this (del). or use `request.delete`
  abort,
} = request
```

Credits
--------
use-http is heavily inspired by the popular http client [axios](https://github.com/axios/axios)

Feature Requests/Ideas
----------------------
If you have feature requests, let's talk about them in [this issue](https://github.com/alex-cory/use-http/issues/13)!

Todos
------
 - [ ] Make work with React Suspense [current example WIP](https://codesandbox.io/s/7ww5950no0)
 - [ ] Allow option to fetch on server instead of just having `loading` state
 - [ ] Allow option for callback for response.json() vs response.text()
 - [ ] add `timeout`
 - [ ] add `debounce`
 - [ ] if 2nd param of `post` or one of the methods is a `string` treat it as query params
 - [ ] error handling if no url is passed
 - [ ] tests
 - [ ] port to typescript
 - [ ] badges, I like the way [these guys do it](https://github.com/GitSquared/edex-ui)
 - [ ] if no url is specified, and we're in the browser, use `window.location.href`
 - [ ] github page/website
 - [ ] get it all working on a codesandbox to test SSR on it, also can have api to call locally
 - [ ] potentially GraphQL support `request.query`, `request.mutate`, `useQuery`, `useMutation`

#### Query <sup>(Not Implemented Yet)</sup>
```jsx
const App = () => {
  const request = useFetch('http://example.com')

  const query = gql`
    query Todos($userID string!) {
      todos(userID: $userID) {
        id
        title
      }
    }
  `

  const getTodosForUser = id => request.query(query, { userID: id })

  return (
    <>
      <button onClick={() => getTodosForUser('theUsersID')}>Get User's Todos</button>
      {!request.loading ? 'Loading...' : <pre>{request.data}</pre>}
    </>
  )
}
```
#### Mutation <sup>(Not Implemented Yet)</sup>
```jsx
const App = () => {
  const [todoTitle, setTodoTitle] = useState('')
  const request = useFetch('http://example.com')

  const mutation = gql`
    mutation CreateTodo($todoTitle string) {
      todo(title: $todoTitle) {
        id
        title
      }
    }
  `

  const createtodo = () => request.mutate(mutation, { todoTitle })

  return (
    <>
      <input onChange={e => setTodoTitle(e.target.value)} />
      <button onClick={createTodo}>Create Todo</button>
      {!request.loading ? 'Loading...' : <pre>{request.data}</pre>}
    </>
  )
}
```
