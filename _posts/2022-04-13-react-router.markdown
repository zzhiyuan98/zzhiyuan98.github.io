---
layout: post
title: How to Use Navigation in React
---
### Install React Router
```shell
npm install react-router-dom
```
### Configre Routes
Suppose we have three pages, Login, Home, and Details. 
```JSX
// App.js
import { BrowserRouter as Router, Route, Routes} from "react-router-dom";
import Login from "./Login";
import Home from "./Home";
import Details from "./Details";

function App() {
  return (
    <div className="app">
      <Router>
        <Routes>
          <Route exact path="/" element={<Login/>} />
          <Route exact path="/home" element={<Home/>} />
          <Route exact path="/details" element={<Details/>} />
        </Routes>
      </Router>
    </div>
  );
}

export default App;
```
### Navigate without passing parammeters
```JSX
// Login.js
import { useNavigate } from "react-router-dom";

function Login() {
  const navigate = useNavigate();
  ...
  <button type="button" onClick={() => navigate("/home")}>
    Login
  </button>
}
export default Login;
```

### Pass parameters to routes
What if we want to pass parameters? Suppose we would like to pass `item` to the details page.
```JSX
// Home.js
import { useNavigate } from "react-router-dom";

function Home() {
  const navigate = useNavigate();
  const item = {
    name: pomegranate, 
    price: $3,
  };
  return (
    ...
    <button type="button" onClick={
      () => navigate("/details", {state: {item: item}})
    }>
      Edit
    </button>
    ...
  );
}
export default Home;
```
```JSX
// Details.js
import { useNavigate, useLocation } from "react-router-dom";

function Details() {
  const navigate = useNavigate();
  const location = useLocation();
  const item = location.state ? location.state.item : null;
  return (
    ...
    Name: {item ? item.name : ""}
    Price: {item ? item.price : ""}
    ...
  );
}
export default Details;
```

### Alternative: \<Link\> to Rescue
We could also use the \<Link\> tag to wrap the element that we want to be clickable.
```JSX
import { Link } from "react-router-dom";
...
<Link to="/details" state=\{\{item: item\}\}>
  <GoPencil size={22}/>
</Link>
...
```

### Back to the Previous Page
```JSX
<button type="button" onClick={() => {navigate(-1);}}>
  Back
</button>
```