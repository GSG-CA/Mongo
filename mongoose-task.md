# Start Using Mongoose | Simple Task
So let's get started right away. Make a new directory `mongo-walkthrough` or whatever name you want and initialize it.

## Setup:
* `mkdir mongo-walkthrough`
* `cd mongo-walkthrough`
* `npm init -y`
* `npm i mongoose env2`
* `npm i -D jest` (oh yeah, you'll never escape testing.)
* `mkdir database database/models database/queries tests`
* `touch .env database/connection.js tests/index.js`

---

## Connection
Now we have the basic project file structure and dependencies ready. Let's start coding.

First, let's make sure we are connected to the mongodb server. 
* Open a new terminal tab and type `mongo`.
* Create a new database `mongo-walkthrough`
* in our `.env` file add the connection link:
>`DB_URL=mongodb://localhost:27017/mongo-walkthrough`
* in `connection.js` let's set up our mongodb connection.    
  **Have a search about it**, check [mongoose connection](https://mongoosejs.com/docs/connections.html)


---

## Models and Quires 
In the models directory, create a `User.js` file.
- create s User model as follows

  ```json
    {
      "firstName": "string",
      "lastName": "string",
      "age": "number",
      "city": "string"
    }
  ```
- in the `queries` directory, create a `users.js` file.
  ```javascript
  // queries/users.js
  const User = require('../models/User');

  const createUser = ({ firstName, lastName, age, city }) => {
    return User.create({ firstName, lastName, age, city });
  };

  const findAll = () => // It's your task 

  const findOne = (name) => // It's your task 

  const deleteAll = () => // It's your task

  module.exports = {
    createUser,
    findAll,
    findOne,
    deleteAll
  };

  ```  

Now we are ready to test if our database is set up properly or not. Take a look at the `createUser` query. It requires a `user` object and returns a query. So let's test it.

In the `test` directory, create a file `users.test.js`

```javascript
// users.test.js
const connection = require('../database/connection');
const { createUser } = require('../database/queries/users');

test('test creating new user', () => {
  const user = {
    firstName: 'Farah',
    lastName: 'Zaqout',
    age: 28,
    city: 'Gaza',
  };
    
    // insert new user
  return createUser(user)
            .then(( res ) => {
                console.log(res);
                expect(res.firstName).toBe(user.firstName);
                expect(res.lastName).toBe(user.lastName);
              });
});

afterAll(() => connection.close());
```  
Now let us change the test to see if the validation works. Change the first name to a number, for example `123`. You will notice that the query still passed and a document was created but without the `firstName` key. 

This means the validation passed, but that is not how we want it to behave. If data is invalid, we want to know that and we want our data to not go in.

The reason why invalid data was ignored is that the keys on a document are not `required` by default. Let's make the firstName required.

- Now your task is to fix the problem above

- Good Practice: delete all entries in your database before running each test case to make it easier to predict outputs.

---

## Joins | Populate

- Now we want to create a `post` models, with the following schema
  ```
    {
      content: String,
      author: // ref to user ID 
    }

  ```

- Now let's write the `posts` queries. In the `queries` directory, create a file `posts.js`

```javascript
// queries/posts.js
const Post = require('../models/Post');


const createPost = ({ content, author }) => {
	return Post.create({ content, author });
};

const findAllPosts = () => {
	return 
};

const deleteAllPosts = () => {
	return 
};

module.exports = {
  createPost,
  findAllPosts,
  deleteAllPosts
};
```
Notice how the `createPost` query is no different from the `createUser`. The `author` here will be the id of a user. Lets test if this will work. Create a `posts.test.js` file.

```javascript
// posts.test.js
const connection = require('../database/connection');
const { deleteAllPosts, createPost } = require('../database/queries/posts');
const { createUser, deleteAll } = require('../database/queries/users');

test('hello', () => {
  const user = {
		firstName: 'Farah',
		lastName: 'Zaqout',
		age: 28,
		city: 'Gaza',
	}; 

    // insert new user
  return deleteAll()
          .then(() => deleteAllPosts())
          .then(() => createUser(user))
          .then((newUser) => createPost({
              content: 'Hello, G11',
              author: newUser._id
          }))
          .then((res) => {
            console.log(res);
            expect(res.content).toBe('Hello, G11');
            expect(res).toHaveProperty('author');
          });
});

afterAll(() => connection.close());

```  

