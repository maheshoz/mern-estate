

```sh
npm create vite@latest client
npm install
npm run dev
```

https://tailwindcss.com/docs/guides/vite

npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```
index.css

@tailwind base;
@tailwind components;
@tailwind utilities;
```
npm run dev

extensions - tailwing intellisence, console ninja, es7 snippets

https://github.com/sahandghavidel/mern-estate


```
npm install react-router-dom
```
Create the pages and routes
```js
import { BrowserRouter, Routes, Route } from "react-router-dom"
import Home from "./pages/Home"
import SignIn from "./pages/SignIn"
import SignUp from "./pages/SignUp"
import About from "./pages/About"
import Profile from "./pages/Profile"

export default function App() {
  return <BrowserRouter>
    <Routes>
      <Route path="/" element={<Home />}/>
      <Route path="/sign-in" element={<SignIn />}/>
      <Route path="/sign-up" element={<SignUp />}/>
      <Route path="/about" element={<About />}/>
      <Route path="/profile" element={<Profile />}/>

    </Routes>
  </BrowserRouter>
}
```

```
npm i react-icons
```
create and add the navbar and installed react-icons package

```js
import {FaSearch} from "react-icons/fa";
import { Link } from "react-router-dom";

export default function Header() {
  return (
    <header className='bg-slate-200 shadow-md'>
      <div>
        <Link to='/'>
         ....
        </Link>
         <form >
          <input type="text" placeholder='Search...'/>
          <FaSearch className="text-slate-600"/>
        </form>

        <ul className="flex gap-4">
          <Link to='/'>
           ..
          </Link>
          <Link to='/about'>
           ..
          </Link>
          <Link to='/sign-in'>
           ..
          </Link>
        </ul>

      </div>
    </header>
  )
}

```

API
- npm init -y
- npm install express
- npm install nodemon or install as devDependency

```js
 "scripts": {
    "dev": "nodemon api/index.js",
    "start": "node api/index.js"
  },
```
npm run dev

### Connect to DB

`npm i mongoose`
login to mongoDB site, create a new project 'mern-estate', create a DB
create username, pswd
select cloud environment
0.0.0.0 ip address to access from anywhere
finish , copy the URI

```js
mongoose.connect("mongodb+srv://username:password@mern-estate.3333r33.mongodb.net/clusterName?retryWrites=true&w=majority")
```
install `dotenv` package , to use `process.env.MONGO_URI`
```sh
npm install dotenv
```

```js
import express from "express";
import mongoose from "mongoose";
import dotenv from "dotenv";
dotenv.config();

mongoose
  .connect(process.env.MONGO)
  .then(() => {
    console.log('Connected to MongoDB');
  })
  .catch((err) => {
    console.log(err);
  });

const app = express();

app.listen(3000, () => {
  console.log("Server is running on port 3000 !");
});

```

### Create user model

```js
import mongoose from "mongoose";

const userSchema = new mongoose.Schema({
  username: {
    type: String,
    required: true,
    unique: true
  },
  email: {
    type: String,
    required: true,
    unique: true
  },
  password: {
    type: string,
    required: true
  }
}, { timestamps: true}); //for created, updated timestamps

const User = mongoose.model('User', userSchema); // use Singular 'User', mongoDB will create Users table/document

export default User;
```

When we do POST to http://localhost:3000/api/auth/signup we get undefined from req.body
we need to allow json parsing in server, add below middleware
```js
app.use(express.json());
```

install bcryptjs to has the password
```js
$ npm i bcryptjs
```

```js
import User from "../models/user.model.js";
import bcryptjs from "bcryptjs";

export const signup = async (req, res) => {
  console.log(req.body);

  const { username, email, password} = req.body;
  const hashedPassword = bcryptjs.hashSync(password, 10)

  const newUser = new User({username, email, password: hashedPassword});

  try {
    await newUser.save()
    res.status(201).json("User created successfully");
    
  } catch (error) {
    res.status(500).json(error.message)
  }
}
```