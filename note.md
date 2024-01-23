

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


create proxy for api's in client
```js
//vite.config.js

export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target : 'http://localhost:3000',
        secure: false,
      },
    },
  },
  plugins: [react()],
})

```


### sigin api route

install jwt
`npm i jsonwebtoken`

add `JWT_SECRET = "asdfa"` in .env file

```js
export const signin = async (req, res, next) => {
  const {email, password} = req.body;
  try {
    const validUser = await User.findOne({email});
    console.log('validUser - ', validUser);
    if(!validUser) return next(errorHandler(404, 'User not found!'));

    const validPassword = bcryptjs.compareSync(password, validUser.password);
    if(!validPassword) return next(errorHandler(401, 'Wrong Credentials!'));

    const token = jwt.sign({id : validUser._id}, process.env.JWT_SECRET);
    console.log(token);
    // res.cookie('access_token', token, { httpOnly: true, expires: new Date(Date.now() + 24 * 60 *60 * 10)}); // expires in 10 days
    // res.cookie('access_token', token, { httpOnly: true}); // will work as session 
    // destructure validuser to remove password keyvalue as we send it in response
    const {password: pass, ...rest} = validUser._doc;
    console.log('pswd - ',pass );
    console.log('destructred data ', rest);
    res
      .cookie('access_token', token, {httpOnly : true})
      .status(200)
      .json(rest);

  } catch (error) {
    next(error);
  }
}
```


### add redux-toolkit to persist user data

https://redux-toolkit.js.org/tutorials/quick-start

installing in the client side
```sh
npm install @reduxjs/toolkit react-redux
```

```js
//store.js
import { configureStore } from "@reduxjs/toolkit";
import userReducer from './user/userSlice.js'

export const store = configureStore({
  reducer: {user: userReducer},
  // middleware to remove browser error for serialzing
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: false,
    }),
});

```

```js
//userSlice.js

import { createSlice } from "@reduxjs/toolkit";

const initialState = {
  currentUser : null,
  error: null,
  loading: false,
};

const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {
    signInStart: (state) => {
      state.loading = true;
    },
    signInSuccess: (state, action) => {
      state.currentUser = action.payload;
      state.loading = false;
      state.error = false;
    },
    signInFailure: (state, action) => {
      state.error = action.payload;
      state.loading = false;
    },
  }
});
console.log(userSlice);

export const { signInStart, signInSuccess, signInFailure} = userSlice.actions;

export default userSlice.reducer;
```

```js
//SignIn.jsx

import { useDispatch, useSelector } from 'react-redux';
import { signInStart, signInSuccess, signInFailure } from '../redux/user/userSlice';

  // const [error, setError] = useState(null);
  // const [loading, setLoading] = useState(false);
  const { loading, error} = useSelector((state) => state.user);
  const dispatch = useDispatch();
 ...

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      // setLoading(true);
      dispatch(signInStart());
      const res = await fetch('/api/auth/signin', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(formData),
      });
      const data = await res.json();
      console.log(data);
      if (data.success === false) {
        // setLoading(false);
        // setError(data.message);
        dispatch(signInFailure(data.message));
        return;
      }
      // setLoading(false);
      // setError(null);
      dispatch(signInSuccess(data));
      navigate('/');
    } catch (error) {
      // setLoading(false);
      // setError(error.message);
      dispatch(signInFailure(error.message));
    }

```

install Redux DevTools chrome extension

on reload, the redux states/snapshots are lost so we will new package redux-persist to have redux state changes

