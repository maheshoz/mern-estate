

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