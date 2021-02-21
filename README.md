![](https://sophosnews.files.wordpress.com/2017/01/mongodb.png?w=780&h=408&crop=1)

# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png)  SOFTWARE ENGINEERING IMMERSIVE

## Getting started

1. Fork
1. Clone

# Mongoose: elegant mongodb object modeling for node.js

"Let's face it, writing MongoDB validation, casting and business logic boilerplate is a drag. That's why we wrote Mongoose." - Mongoose Team

"Mongoose provides a straight-forward, schema-based solution to model your application data. It includes built-in type casting, validation, query building, business logic hooks and more, out of the box." - Mongoose Team

"Mongoose is a popular helper library that provides a more rigorous modeling environment for your data, enforcing a little bit more structure as needed, while still maintaining flexibility that makes MongoDB powerful." - Heroku

> Let's take five minutes and read about Mongoose: 
>
> - [What is Mongoose?](https://www.freecodecamp.org/news/introduction-to-mongoose-for-mongodb-d2a7aa593c57/)

Great! So how do we use Mongo with Mongoose!? Let's build a user database using Mongo and Mongoose:

```sh
cd mongodb-mongoose
npm init -y
npm install mongoose
mkdir db models seed
touch db/index.js models/user.js seed/users.js
```

Now let's open up Visual Studio Code and write some code:

```sh
code .
```

Inside our `db` folder we are going to use Mongoose to establish a connection to our MongoDB `usersDatabase`:

mongodb-mongoose/db/index.js
```js
const mongoose = require('mongoose')

mongoose
    .connect('mongodb://127.0.0.1:27017/usersDatabase', { useUnifiedTopology: true, useNewUrlParser: true })
    .then(() => {
        console.log('Successfully connected to MongoDB.');
      })
    .catch(e => {
        console.error('Connection error', e.message)
    })

const db = mongoose.connection

module.exports = db
```

Although, MongoDB is schema-less, Mongoose allows us to write a schema for our user model which makes it nice to know what is a user in our database and what a user "looks" like in our database:

mongodb-mongoose/models/user.js
```js
const mongoose = require('mongoose')
const Schema = mongoose.Schema

const User = new Schema(
    {
        name: { type: String, required: true },
        age: { type: Number, required: true },
        status: { type: String, required: true },
    },
    { timestamps: true },
)

module.exports = mongoose.model('users', User)
```

Cool. We have a "blueprint" for what a user is. Let's now use it and create users.

mongodb-mongoose/seed/users.js
```js
const db = require('../db')
const User = require('../models/user')

db.on('error', console.error.bind(console, 'MongoDB connection error:'))

const main = async () => {
    const users = [
        { name: 'Benny', age: 28, status: 'active' },
        { name: 'Claire', age: 28, status: 'active' },
        { name: 'Joey', age: 28, status: 'active' },
        { name: 'Abe', age: 22, status: 'pending' },
        { name: 'Sunny', age: 23, status: 'pending' },
        { name: 'Lizzy', age: 28, status: 'active' },
        { name: 'Julie', age: 21, status: 'active' }
    ]

    await User.insertMany(users)
    console.log("Created users!")
}

const run = async () => {
    await main()
    db.close()
}

run()
```

Awesome, so this users "seed" file above is a script that, once executed, connects to the Mongo database and creates 7 users in the users collection.

Let's execute our users seed file:

```sh
node seed/users.js
```

So how do we know if it worked? We could drop into the `mongo` interactive shell and check:

```sh
mongo
> use usersDatabase
> db.users.find()
```

OR we could use Mongoose to write a JavaScript query to find all users:

```sh
touch query.js
```

mongodb-mongoose/query.js
```js
// https://mongoosejs.com/docs/queries.html

const db = require('./db')
const User = require('./models/user')

// Connect to the database
db.on('error', console.error.bind(console, 'MongoDB connection error:'))

const findAll = async () => {
    const users = await User.find()
    console.log("All users:", users)
}

const run = async () => {
    await findAll()
    process.exit()
}

run()
```

Now let's run this and confirm that our users seed file worked:

```sh
node query.js
```

Below you'll see the complete query file for performing CRUD actions against a MongoDB database using Mongoose:

mongodb-mongoose/query.js
```js
// https://mongoosejs.com/docs/queries.html

const db = require('./db')
const User = require('./models/user')

db.on('error', console.error.bind(console, 'MongoDB connection error:'))

const findAll = async () => {
    const users = await User.find()
    console.log("All users:", users)
}

const createUser = async () => {
    const user = new User({ name: 'Carol', age: 24, status: 'active' })
    await user.save()
    console.log("Created user:", user)
}

const deleteUser = async () => {
    const deleted = await User.deleteOne({ name: "Carol" })
    console.log(deleted)
}

const updateUser = async () => {
    const updated = await User.updateOne({ name: "Benny"}, { name: "Benjamin"})
    console.log(updated)
}

const findAllNames = async () => {
    const names = await User.find({}, 'name')
    console.log(names)
}

const findAllOlderThan25 = async () => {
    const usersOlderThan25 = await User.find({}).where('age').gt(25)
    console.log(usersOlderThan25)
}

const activeAndLessThan25 = async () => {
    const activeAndLessThan25 = await User.find({ status: "active" }).where("age").lt(25)
    console.log(activeAndLessThan25)
}

const run = async () => {
    await findAll()
    // await createUser()
    // await deleteUser()
    // await updateUser()
    // await findAllNames()
    // await findAllOlderThan25()
    // await activeAndLessThan25()
    process.exit()
}

run()
```

Become familiar with executing the above and writing MongoDB queries using Mongoose. We will be using this setup for the rest of the course.
> https://mongoosejs.com/docs/queries.html

But wait! There is this really cool tool we would like to share with you that will make your life easier as a developer!

### MongoDB Compass: The easiest way to explore and manipulate your MongoDB data

![](https://docs.mongodb.com/compass/master/_images/query-limit-success.png)

Take a couple minutes and review MongoDB Compass:
> https://www.mongodb.com/products/compass

Then let's all download and install MongoDB Compass [here](https://www.mongodb.com/download-center/compass)

You now have the freedom to use Compass at your disposal. But remember! Mastering the `mongo` shell is key - as a developer we find ourselves dropping into the `mongo` shell quite often.

Don't forget to create a `.gitignore` file before pushing up code to GitHub:

```sh
echo "
/node_modules
.DS_Store" >> .gitignore
```

Done!

## Resources

- https://mongoosejs.com/docs/queries.html
- https://docs.mongodb.com/manual

## Feedback

> [Take a minute to give us feedback on this lesson so we can improve it!](https://forms.gle/vgUoXbzxPWf4oPCX6)
