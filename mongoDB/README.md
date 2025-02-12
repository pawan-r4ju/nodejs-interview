# 3 PROJECTS USING MONGOOSE(MONGO DB)
### **1\. Todo List API**

**Description**: Build a simple REST API for managing a todo list. Users can create, read, update, and delete tasks.

**Features**:

-   Create a new task (title, description, status).

-   Get all tasks.

-   Update a task (mark as completed).

-   Delete a task.

**Steps**:

1.  Set up an Express server.

2.  Define a Mongoose schema for tasks (`title`, `description`, `status`).

3.  Create routes for:

    -   `POST /tasks` (create a task).

    -   `GET /tasks` (get all tasks).

    -   `PUT /tasks/:id` (update a task).

    -   `DELETE /tasks/:id` (delete a task).

4.  Test the API using Postman or Thunder Client.

* * * * *

### **2\. User Authentication API**

**Description**: Build a basic user authentication system with registration and login.

**Features**:

-   Register a new user (name, email, password).

-   Login with email and password (return a success message or token).

**Steps**:

1.  Set up an Express server.

2.  Define a Mongoose schema for users (`name`, `email`, `password`).

3.  Hash passwords using `bcrypt` before saving to the database.

4.  Create routes for:

    -   `POST /register` (register a new user).

    -   `POST /login` (authenticate a user).

5.  Test the API using Postman or Thunder Client.

* * * * *

### **3\. Bookstore Inventory API**

**Description**: Build a simple API to manage a bookstore's inventory.

**Features**:

-   Add a new book (title, author, price, quantity).

-   Get all books.

-   Update a book's details.

-   Delete a book.

**Steps**:

1.  Set up an Express server.

2.  Define a Mongoose schema for books (`title`, `author`, `price`, `quantity`).

3.  Create routes for:

    -   `POST /books` (add a new book).

    -   `GET /books` (get all books).

    -   `PUT /books/:id` (update a book).

    -   `DELETE /books/:id` (delete a book).

4.  Test the API using Postman or Thunder Client.

* * * * *

### **Tips for the Interview**:

-   Focus on clean code and proper folder structure (e.g., separate routes, models, and controllers).

-   Use async/await for database operations.

-   Handle errors gracefully (e.g., invalid IDs, missing fields).

-   Test your API thoroughly during the interview to show confidence.

Good luck! 🚀

### **1\. Todo List API**

#### **File Structure**:


```bash
todo-api/
├── controllers/
│   └── taskController.js
├── models/
│   └── Task.js
├── routes/
│   └── taskRoutes.js
└── app.js
```
#### **Code**:


### `app.js`
```bash
const express = require('express');
const mongoose = require('mongoose');
const taskRoutes = require('./routes/taskRoutes');
require('dotenv').config();

const app = express();
app.use(express.json());

mongoose.connect(process.env.MONGODB_URI)
  .then(() => console.log('Connected to MongoDB'))
  .catch(err => console.error(err));

app.use('/tasks', taskRoutes);

const PORT = 3000;
app.listen(PORT, () => console.log(`Todo API running on port ${PORT}`));
```


### `models/Task.js`
```bash
const mongoose = require('mongoose');

const taskSchema = new mongoose.Schema({
  title: { type: String, required: true },
  description: String,
  status: { type: String, default: 'pending' }
});

module.exports = mongoose.model('Task', taskSchema);
```
### `controllers/taskController.js`
```bash

const Task = require('../models/Task');

exports.createTask = async (req, res) => {
  try {
    const task = await Task.create(req.body);
    res.status(201).json(task);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};

exports.getAllTasks = async (req, res) => {
  try {
    const tasks = await Task.find();
    res.json(tasks);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
};

exports.updateTask = async (req, res) => {
  try {
    const task = await Task.findByIdAndUpdate(req.params.id, req.body, { new: true });
    res.json(task);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};

exports.deleteTask = async (req, res) => {
  try {
    await Task.findByIdAndDelete(req.params.id);
    res.json({ message: 'Task deleted' });
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};
```
### `routes/taskRoutes.js`
```bash
const express = require('express');
const router = express.Router();
const taskController = require('../controllers/taskController');

router.post('/', taskController.createTask);
router.get('/', taskController.getAllTasks);
router.put('/:id', taskController.updateTask);
router.delete('/:id', taskController.deleteTask);

module.exports = router;
```

* * * * *

### **2\. User Authentication API**

#### **File Structure**:

Copy
```bash
auth-api/
├── controllers/
│   └── authController.js
├── models/
│   └── User.js
├── routes/
│   └── authRoutes.js
└── app.js
```
#### **Code**:



### `app.js`
```bash
const express = require('express');
const mongoose = require('mongoose');
const authRoutes = require('./routes/authRoutes');
require('dotenv').config();

const app = express();
app.use(express.json());

mongoose.connect(process.env.MONGODB_URI)
  .then(() => console.log('Connected to MongoDB'))
  .catch(err => console.error(err));

app.use('/auth', authRoutes);

const PORT = 3001;
app.listen(PORT, () => console.log(`Auth API running on port ${PORT}`));
```

### `models/User.js`
```bash
const mongoose = require('mongoose');
const bcrypt = require('bcrypt');

const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true }
});

userSchema.pre('save', async function (next) {
  if (this.isModified('password')) {
    this.password = await bcrypt.hash(this.password, 10);
  }
  next();
});

module.exports = mongoose.model('User', userSchema);
```


### `controllers/authController.js`
```bash
const User = require('../models/User');
const bcrypt = require('bcrypt');

exports.register = async (req, res) => {
  try {
    const user = new User(req.body);
    await user.save();
    res.status(201).json({ message: 'User registered' });
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};

exports.login = async (req, res) => {
  try {
    const user = await User.findOne({ email: req.body.email });
    if (!user) return res.status(400).json({ message: 'Invalid credentials' });

    const validPassword = await bcrypt.compare(req.body.password, user.password);
    if (!validPassword) return res.status(400).json({ message: 'Invalid credentials' });

    res.json({ message: 'Login successful' });
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
};
```
### `routes/authRoutes.js`
```bash
const express = require('express');
const router = express.Router();
const authController = require('../controllers/authController');

router.post('/register', authController.register);
router.post('/login', authController.login);

module.exports = router;
```
* * * * *

### **3\. Bookstore Inventory API**

#### **File Structure**:

Copy
```bash
bookstore-api/
├── controllers/
│   └── bookController.js
├── models/
│   └── Book.js
├── routes/
│   └── bookRoutes.js
└── app.js
```
#### **Code**:



### `app.js`
```bash
const express = require('express');
const mongoose = require('mongoose');
const bookRoutes = require('./routes/bookRoutes');
require('dotenv').config();

const app = express();
app.use(express.json());

mongoose.connect(process.env.MONGODB_URI)
  .then(() => console.log('Connected to MongoDB'))
  .catch(err => console.error(err));

app.use('/books', bookRoutes);

const PORT = 3002;
app.listen(PORT, () => console.log(`Bookstore API running on port ${PORT}`));
```


### `models/Book.js`
```bash
const mongoose = require('mongoose');

const bookSchema = new mongoose.Schema({
  title: { type: String, required: true },
  author: { type: String, required: true },
  price: { type: Number, required: true },
  quantity: { type: Number, default: 0 }
});

module.exports = mongoose.model('Book', bookSchema);
```


### `controllers/bookController.js`
```bash
const Book = require('../models/Book');

exports.createBook = async (req, res) => {
  try {
    const book = await Book.create(req.body);
    res.status(201).json(book);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};

exports.getAllBooks = async (req, res) => {
  try {
    const books = await Book.find();
    res.json(books);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
};

exports.updateBook = async (req, res) => {
  try {
    const book = await Book.findByIdAndUpdate(req.params.id, req.body, { new: true });
    res.json(book);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};

exports.deleteBook = async (req, res) => {
  try {
    await Book.findByIdAndDelete(req.params.id);
    res.json({ message: 'Book deleted' });
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
};
```
### `routes/bookRoutes.js`
```bash
const express = require('express');
const router = express.Router();
const bookController = require('../controllers/bookController');

router.post('/', bookController.createBook);
router.get('/', bookController.getAllBooks);
router.put('/:id', bookController.updateBook);
router.delete('/:id', bookController.deleteBook);

module.exports = router;
```
* * * * *

### **How to Run**:

1.  Create a `.env` file in each project folder with:


```bash
    MONGODB_URI=mongodb://localhost:27017/[database-name]
```
2.  Install dependencies:

```bash
    npm init -y
    npm install express mongoose dotenv bcrypt
```
3.  Start each project in separate terminals:

```bash
    node todo-api/app.js
    node auth-api/app.js
    node bookstore-api/app.js
```
### **Testing Endpoints** (Use Postman):

-   **Todo API**:

    -   `POST http://localhost:3000/tasks` (Body: JSON with `title`, `description`)

    -   `GET http://localhost:3000/tasks`

-   **Auth API**:

    -   `POST http://localhost:3001/auth/register` (Body: JSON with `name`, `email`, `password`)

    -   `POST http://localhost:3001/auth/login` (Body: JSON with `email`, `password`)

-   **Bookstore API**:

    -   `POST http://localhost:3002/books` (Body: JSON with `title`, `author`, `price`, `quantity`)

    -   `GET http://localhost:3002/books`

All three projects demonstrate CRUD operations, error handling, and Mongoose model validation. They can be easily extended with additional features if time permits! 🚀

# assessment questions

### Basic Questions (15)

1.  What is MongoDB?

    -   MongoDB is a NoSQL database that stores data in flexible, JSON-like documents.
    -   It is schema-less and supports dynamic queries, making it ideal for unstructured or semi-structured data.
2.  What is a document in MongoDB?

    -   A document is a BSON (Binary JSON) object that represents a single record in MongoDB.
    -   Documents are stored in collections, similar to rows in relational databases.
3.  What is a collection in MongoDB?

    -   A collection is a group of documents stored in MongoDB, analogous to tables in relational databases.
    -   Collections do not enforce a strict schema, allowing flexibility in document structure.
4.  What is Mongoose?

    -   Mongoose is an ODM (Object Data Modeling) library for MongoDB and Node.js.
    -   It provides schema validation, middleware, and easier querying for MongoDB.
5.  What is the difference between SQL and NoSQL databases?

    -   SQL databases use structured tables with predefined schemas, while NoSQL databases like MongoDB store data in flexible documents.
    -   NoSQL databases are better suited for scalability and handling unstructured data.
6.  What is BSON in MongoDB?

    -   BSON (Binary JSON) is a binary-encoded serialization of JSON-like documents used by MongoDB.
    -   It supports additional data types like dates and binary data, which are not natively supported in JSON.
7.  How do you connect to a MongoDB database using Mongoose?

    -   Use `mongoose.connect('mongodb://<connection-string>')` to establish a connection.
    -   Example: `mongoose.connect('mongodb://localhost:27017/mydb')`.
8.  What is the purpose of the `_id` field in MongoDB?

    -   The `_id` field is a unique identifier for each document in a collection.
    -   If not provided, MongoDB automatically generates an ObjectId for the `_id`.
9.  What is an ObjectId in MongoDB?

    -   An ObjectId is a 12-byte identifier used as the default value for the `_id` field.
    -   It consists of a timestamp, machine identifier, process ID, and random value.
10. How do you insert a document into a MongoDB collection?

    -   Use the `insertOne()` or `insertMany()` methods.
    -   Example: `db.collection.insertOne({ name: "John", age: 30 })`.
11. How do you query documents in MongoDB?

    -   Use the `find()` method with optional filter criteria.
    -   Example: `db.collection.find({ age: { $gt: 25 } })`.
12. What is the `find()` method in MongoDB?

    -   The `find()` method retrieves all documents that match a query.
    -   It returns a cursor, which can be iterated to access results.
13. What is the `findOne()` method in MongoDB?

    -   The `findOne()` method retrieves the first document that matches a query.
    -   Example: `db.collection.findOne({ name: "John" })`.
14. How do you update a document in MongoDB?

    -   Use the `updateOne()` or `updateMany()` methods with a filter and update operation.
    -   Example: `db.collection.updateOne({ name: "John" }, { $set: { age: 35 } })`.
15. How do you delete a document in MongoDB?

    -   Use the `deleteOne()` or `deleteMany()` methods with a filter.
    -   Example: `db.collection.deleteOne({ name: "John" })`.

* * * * *

### Intermediate Questions (20)

1.  What is indexing in MongoDB?

    -   Indexing improves query performance by creating a data structure that allows faster lookups.
    -   Example: `db.collection.createIndex({ name: 1 })`.
2.  What is the difference between `createIndex` and `ensureIndex`?

    -   `createIndex` creates a new index if it doesn't exist, while `ensureIndex` is deprecated and replaced by `createIndex`.
    -   Always use `createIndex` in modern MongoDB versions.
3.  What is aggregation in MongoDB?

    -   Aggregation processes data records and returns computed results, such as sums, averages, or grouped data.
    -   Example: `db.collection.aggregate([{ $group: { _id: "$category", total: { $sum: "$price" } } }])`.
4.  What is the `$match` stage in aggregation?

    -   The `$match` stage filters documents based on specified criteria.
    -   Example: `{ $match: { age: { $gt: 25 } } }`.
5.  What is the `$group` stage in aggregation?

    -   The `$group` stage groups documents by a specified key and performs calculations like sum or average.
    -   Example: `{ $group: { _id: "$category", total: { $sum: "$price" } } }`.
6.  What is sharding in MongoDB?

    -   Sharding distributes data across multiple servers to improve scalability and performance.
    -   It splits large datasets into smaller chunks called shards.
7.  What is replication in MongoDB?

    -   Replication maintains multiple copies of data across different servers for fault tolerance.
    -   A replica set consists of primary and secondary nodes.
8.  What is the role of the primary node in a replica set?

    -   The primary node handles all write operations and replicates changes to secondary nodes.
    -   Secondary nodes can serve read operations if configured.
9.  What is the `populate()` method in Mongoose?

    -   The `populate()` method replaces references (e.g., ObjectIds) with actual documents from another collection.
    -   Example: `User.findById(id).populate('posts')`.
10. What is a schema in Mongoose?

    -   A schema defines the structure of documents, including fields, data types, and validation rules.
    -   Example: `const userSchema = new mongoose.Schema({ name: String, age: Number })`.
11. What is middleware in Mongoose?

    -   Middleware are functions executed before or after certain lifecycle events, such as saving or validating a document.
    -   Example: `schema.pre('save', function(next) { ... })`.
12. What are virtuals in Mongoose?

    -   Virtuals are document properties that are not stored in the database but computed dynamically.
    -   Example: `userSchema.virtual('fullName').get(() =>` this.firstName{this.lastName}`)`.
13. What is the difference between `save()` and `insertMany()` in Mongoose?

    -   `save()` persists a single document instance, while `insertMany()` inserts multiple documents at once.
    -   Use `insertMany()` for bulk inserts to improve performance.
14. What is the purpose of the `timestamps` option in Mongoose?

    -   The `timestamps` option automatically adds `createdAt` and `updatedAt` fields to documents.
    -   Example: `const schema = new Schema({}, { timestamps: true })`.
15. What is the difference between `findByIdAndUpdate` and `findOneAndUpdate`?

    -   `findByIdAndUpdate` updates a document by its `_id`, while `findOneAndUpdate` uses any filter criteria.
    -   Example: `Model.findByIdAndUpdate(id, { $set: { name: "John" } })`.
16. What is the `lean()` method in Mongoose?

    -   The `lean()` method returns plain JavaScript objects instead of Mongoose documents, improving performance.
    -   Example: `Model.find().lean()`.
17. What is the `distinct()` method in MongoDB?

    -   The `distinct()` method retrieves unique values for a specified field.
    -   Example: `db.collection.distinct("category")`.
18. What is the `$lookup` stage in aggregation?

    -   The `$lookup` stage performs a left outer join with another collection.
    -   Example: `{ $lookup: { from: "orders", localField: "userId", foreignField: "_id", as: "userOrders" } }`.
19. What is the `$unwind` stage in aggregation?

    -   The `$unwind` stage deconstructs an array field into separate documents for each element.
    -   Example: `{ $unwind: "$tags" }`.
20. What is the difference between `drop()` and `deleteMany()`?

    -   `drop()` deletes an entire collection, while `deleteMany()` removes specific documents.
    -   Use `drop()` with caution, as it cannot be undone.

* * * * *

### Advanced Questions (15)

1.  What is TTL (Time-To-Live) in MongoDB?

    -   TTL indexes automatically remove documents after a specified duration.
    -   Example: `db.collection.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })`.
2.  What is the `explain()` method in MongoDB?

    -   The `explain()` method provides detailed information about query execution plans.
    -   Example: `db.collection.find().explain("executionStats")`.
3.  What is the difference between `capped collections` and regular collections?

    -   Capped collections have a fixed size and overwrite old documents when full, while regular collections grow indefinitely.
    -   Use capped collections for logging or caching.
4.  What is the `bulkWrite()` method in MongoDB?

    -   The `bulkWrite()` method performs multiple write operations in a single request.
    -   Example: `db.collection.bulkWrite([{ insertOne: { document: { name: "John" } } }])`.
5.  What is the `changeStream` API in MongoDB?

    -   The `changeStream` API listens for real-time changes to documents in a collection.
    -   Example: `const changeStream = db.collection.watch()`.
6.  What is the difference between `embedded` and `referenced` relationships in MongoDB?

    -   Embedded relationships store related data within the same document, while referenced relationships link documents via ObjectIds.
    -   Use embedding for one-to-few relationships and referencing for one-to-many.
7.  What is the `text` index in MongoDB?

    -   A `text` index enables full-text search on string fields.
    -   Example: `db.collection.createIndex({ description: "text" })`.
8.  What is the `hint()` method in MongoDB?

    -   The `hint()` method forces MongoDB to use a specific index for a query.
    -   Example: `db.collection.find().hint({ name: 1 })`.
9.  What is the `collation` option in MongoDB?

    -   The `collation` option specifies language-specific rules for string comparison, such as case insensitivity.
    -   Example: `db.collection.find({}, { collation: { locale: "en", strength: 2 } })`.
10. What is the `gridfs` module in MongoDB?

    -   GridFS is a specification for storing and retrieving large files, such as images or videos, exceeding the BSON size limit.
    -   It splits files into chunks and stores them in separate collections.
11. What is the `pre` and `post` middleware in Mongoose?

    -   `pre` middleware runs before an operation (e.g., save), while `post` middleware runs after.
    -   Example: `schema.post('save', function(doc) { console.log('Saved:', doc) })`.
12. What is the `discriminator` feature in Mongoose?

    -   Discriminators allow multiple schemas to share the same collection but differ in structure.
    -   Example: `const subSchema = new Schema({ type: String }); const SubModel = Base.discriminator('Sub', subSchema)`.
13. What is the `versionKey` in Mongoose?

    -   The `versionKey` (`__v`) tracks the version of a document to handle optimistic concurrency control.
    -   You can disable it using `versionKey: false` in the schema options.
14. What is the `aggregate()` method in Mongoose?

    -   The `aggregate()` method performs complex data transformations using the MongoDB aggregation pipeline.
    -   Example: `Model.aggregate([{ $group: { _id: "$category", total: { $sum: "$price" } } }])`.
15. What is the future of MongoDB and Mongoose?

    -   MongoDB continues to evolve with features like serverless deployments, enhanced analytics, and improved security.
    -   Mongoose is expected to support more advanced TypeScript integrations and performance optimizations.