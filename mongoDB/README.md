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