1. create a folder - BookingApp
2. open in code editor - VS code
3. Create a folder for backend - server
4. Create a file - index.js
5. Open terminal  `ctrl + shift + ~`
6. In terminal, go to server folder `cd server`
7. make sure Node Js and Npm is installed in your system by running the command  `node -v`, then followerd by `npm -v`
8. If the version is not shown go to https://nodejs.org/en/download/ and install and try step 7 to confirm it's installed
9. Once it's installed, Initialize npm by running the following command in terminal: `npm init -y` (-y represents 'yes' to everything)
10. Now inside server folder you can see `package.json` file open it. Inside it, below "main" type the following:
> "type": "module"
11. Inside script replace it with the following:
> "scripts": {  
>    "start": "nodemon index.js"  
>  },
12. In step 11, We are running the backend with nodemon package, so that we dont have to restart server every single time.
13. To run this we should first installing nodemon. Type the following in the terminal> server - `npm i nodemon`
14. Open index.js in server folder and type the following
```
import express from "express";  
  
const app = express();  
   
app.listen(8000, ()=>{  
  console.log("server running");  
});  
```
15. Now let's setup our mongoDB database. Go to https://cloud.mongodb.com/
16. On top left, (Look at the below image,for reference - highlighted in red)  
 ![step16](https://user-images.githubusercontent.com/69050759/187870600-6e88ff48-2a73-45d2-bfa6-28f24d5d8ff2.png)  
> Open the dropdown, Give a name to your database, Press the `new project` button.
17. Next page, leave the default setting as it is and pres the button at bottom - `create cluster`  
18. Give a usename and password, then press the button - `Create User`  
19. On the same page, give or IP address or press the button - `add my current IP address`  
20. Enter - `Finish and close` button  
21. Next page click `connect` button and select `connect your application` and copy the mongodb uri  
22. Back to VS code, create a file called `.env`  
23. paste the code like the following:   `MONGO = <paste the mondodb uri you copied> ` and give the password and database name  
24. eg: mongodb+...://<database name>:<password>@.......mongodb.net/<database name>?retryWrites=true&w=majority
25. From step 24, in place of <> fill the necessary details  
26. In server terminal install the following packages: `npm i dotenv mongoose`
27. Update index.js as follows:
  ```
import express from "express";
import dotenv from "dotenv";
import mongoose from "mongoose";
const app = express();
dotenv.config();

const connect = async () => {
    try {
        await mongoose.connect(process.env.MONGO);
        console.log("connected to mongoDB+-");
    } catch (error) {
        throw error;
    }
};

mongoose.connection.on("disconnected", ()=>{
    console.log("MongoDB disconnected");
});

mongoose.connection.on("connected", ()=>{
    console.log("MongoDB connected");
});

app.listen(8000, ()=>{
    connect();
    console.log("server running");
});
  ```  
> Inside 'connect' is our initial connection where we connect our mongoDB to our project using the secret evn which has the DB url
> followed by disconnection - will send a msg if it is disconnected
> and connection if its connected.
28. Now add the folowing code above app.listen in package.json:
  ```
  app.get("/", (req,res) => {
    res.send("My first request");
  });
  ```
  > This is how api works: app.get  
  > followed by and endpoint ("/" -> home route  
  > here express will take the request and use it inside API request  
  > Then we are sending the response with some message  
29. Now what if you want to use 100's of Api? to many it simple let's delete unnecessage lines from index.js and create new folder and paste in it.  
30. updated `index.js`  
  ```
  import express from "express";
import dotenv from "dotenv";
import mongoose from "mongoose";
const app = express();
dotenv.config();

const connect = async () => {
    try {
        await mongoose.connect(process.env.MONGO);
        console.log("connected to mongoDB");
    } catch (error) {
        throw error;
    }
};

mongoose.connection.on("disconnected", ()=>{
    console.log("MongoDB disconnected");
});

app.listen(8000, ()=>{
    connect();
    console.log("server running");
});
  ```  
31. create a folder inside server as `routes` and create the following files: `auth.js`, `hotels.js`, `rooms.js`, `users.js`  
32. For authenthication we would use a specific route called auth.js, as we would be using cookies, json web token,..  
33. in `auth.js` type:
  ```
  import express from "express"; //importing express

const router =  express.Router();

// api request
router.get("/", (req, res) => {
    res.send("Hello, this is auth endpoint");
});

export default router; //exporting router
  ```
34. importing `auth.js` in `index.js`
  ```
  import router from "./routes/auth.js";

  //middlewares
  app.use("/auth", router);
  ```
  >app.use must be above app.listen  
35. Just like auth paste the following code in hotels, rooms, users:  
  ```
  import express from "express";

  const router =  express.Router();

  export default router;  
  ```  
  36. Paste the following in index.js inside server  
  ```  
import hotelsRouter from "./routes/hotels.js";
import roomsRouter from "./routes/rooms.js";
import usersRouter from "./routes/users.js";
  
  
//middlewares
app.use(express.json) // as we won't be able to send json directly

app.use("/hotels", hotelsRouter);  
app.use("/rooms", roomsRouter);  
app.use("/users", usersRouter);  
  ```
  37. Node.js MongoDB CRUD Operations in hotels  
For that we need models so create a folder called `models` inside `server` and create following files: `Hotels.js`, `Users.js` and `Rooms.js`
38. We will create a schema in `Hotels.js`. refer: https://mongoosejs.com/docs/guide.html - Defining your schema  
  Paste the following: 
  
  ```
  import mongoose from "mongoose";
const { Schema } = mongoose;

const HotelSchema = new mongoose.Schema({
    name:{
        type: String,
        require: true
    },
    type:{
        type: String,
        require: true
    },
    city:{
        type: String,
        require: true
    },
    address:{
        type: String,
        require: true
    },
    distance:{
        type: String,
        require: true
    },
    photos:{
        type: [String],
    },
    desc:{
        type: String,
        require: true
    },
    rating:{
        type: Number,
        min: 0,
        max: 5
    },
    rooms:{
        type: [String],
    },
    cheapestPrice:{
        type: Number,
        require: true
    },
    featured:{
        type: Boolean,
        require: false
    },

});

export default mongoose.model("Hotel", HotelSchema);
  ```
39. Back to `server>routes>hotel.js`, Paste the following: 
  ```
  import express from "express";
import Hotel from "../models/Hotel.js";

const router =  express.Router();

// Node.js MongoDB CRUD Operations
// CREATE
router.post("/", async (req, res) => {

    const newHotel = new Hotel(req.body)

    try {
        const savedHotl = await newHotel.save();
        res.status(200).json(savedHotl);
    } catch(err) {
        res.status(500).json(err);
    }
});

// UPDATE
router.put("/:id", async (req, res) => {
    try {
        const updateHotel = await Hotel.findByIdAndUpdate(req.params.id, { $set: req.body }, { new : true});
        res.status(200).json(updateHotel);
    } catch(err) {
        res.status(500).json(err);
    }
});

// DELETE
router.delete("/:id", async (req, res) => {
    try {
        await Hotel.findByIdAndDelete(req.params.id);
        res.status(200).json("Hotel has been deleted");
    } catch(err) {
        res.status(500).json(err);
    }
});

// GET
router.get("/:id", async (req, res) => {
    try {
        const hotel = await Hotel.findById(req.params.id);
        res.status(200).json(hotel);
    } catch(err) {
        res.status(500).json(err);
    }
});

// GET ALL
router.get("/", async (req, res) => {
    try {
        const hotels = await Hotel.find();
        res.status(200).json(hotels);
    } catch(err) {
        res.status(500).json(err);
    }
});

export default router;
  ```  
  try sending a request in `insomia/postman`  
  40. Express Middleware  
  Middleware are important because it's able to reach to request and response before sending anything to user  
  . You can write a middleware (say, index.js) and pass it to any page (eg:hotels.js)  
  . You can give mustiple middlewared, but it will be executed from top to bottom  
  41. Express Error Handling  
. Paste the following in `index.js` just before `app.listen`
  ```
  
app.use((err, req, res, next) => {
    const errorStatus = err.status || 500;
    const errorMessage = err.status || "Something went wrong!";
    return req.status(errorStatus).json({
        success: false,
        status: errorStatus,
        message: errorMessage,
        stack: err.stack,
    });
});
  ```
  42. In `server` folder create a folder called `util` an create a file inside it: `error.js` and paste the following:
  ```
  export const createError = (status, message) => {
    const err = new Error();
    err.status = status;
    err.message = message;
    return err;
}
  ```  
    
 ##    MongoDB Authentication(Login / Register)
  43. before that let's create a seperate files for our operations such as crud  
  44. Create a folder inside server called as `controller`
  45. Inside `controller` folder create files such as `hotel.js`, `room.js`, `user.js` and `auth.js`  
  46. paste the following in controllers>hotel.js
  ```
  import express from "express";
import { createHotel, deleteHotel, getHotel, getHotels, updateHotel } from "../controllers/hotel.js";
import Hotel from "../models/Hotel.js";

const router =  express.Router();

// Node.js MongoDB CRUD Operations
// CREATE
router.post("/", createHotel);

// UPDATE
router.put("/:id", updateHotel);

// DELETE
router.delete("/:id", deleteHotel);

// GET
router.get("/:id", getHotel);

// GET ALL
router.get("/", getHotels);

export default router;
  ```
  
47. Now we dont have to do the crud operation directly as we pasted it in `controller>hotel.js` so lets update `routes>hotels.js`
  ```
  import express from "express";
import { createHotel, deleteHotel, getHotel, getHotels, updateHotel } from "../controllers/hotel.js";
import Hotel from "../models/Hotel.js";

const router =  express.Router();

// Node.js MongoDB CRUD Operations
// CREATE
router.post("/", createHotel);

// UPDATE
router.put("/:id", updateHotel);

// DELETE
router.delete("/:id", deleteHotel);

// GET
router.get("/:id", getHotel);

// GET ALL
router.get("/", getHotels);

export default router;
  ```
  48. `controller>auth.js`
  ```
  import User from "../models/User.js"

export const register = async (req, res, next) => {
    try {
        const newUser = new User({
            username:req.body.username,
            email:req.body.email,
            password:req.body.password,
        })
        await newUser.save()
        res.status(200).send("User has been created.");
    } catch (err) {
        next(err)
    }
}
  ```
  49. `route>auth.js`
  ```
  import express from "express"; //importing express
import { register } from "../controllers/auth.js";

const router =  express.Router();

// api request
router.post("/register", register);

export default router; //exporting router
  ```  
   If your using `postman/insomnia` try getting a post request and see if this works
  50. Now to make our users password more secure lets use a npm package.  
  . open new terminal and run the following command in server
  > npm i bcryptjs
  51. Update it in `controller>auth.js`
  ```
import User from "../models/User.js";
import bcrypt from "bcryptjs";

export const register = async (req, res, next) => {
    try {
        const salt = bcrypt.genSaltSync(10);
        const hash = bcrypt.hashSync(req.body.password, salt);

        const newUser = new User({
            username:req.body.username,
            email:req.body.email,
            password:hash,
        })
        await newUser.save()
        res.status(200).send("User has been created.");
    } catch (err) {
        next(err)
    }
}
  ```
  52. Now let's add login and make sure the encrypted password matches with what user login  
  `controller>auth.js`
```
  export const login = async (req, res, next) => {
    try {
        const user = await User.findOne({username:req.body.username})
        if(!user) return next(createError(404, "User not found!"));
        
        const isPasswordCorrect = await bcrypt.compare(req.body.password, user.password);
        if(!isPasswordCorrect) return next(createError(400, "Wrong Password or Username!"));
        
        const { password, isAdmin, ...otherDetails } = user._doc;
        
        res.status(200).json({...otherDetails});
    } catch (err) {
        next(err)
    }
}
  ```
  53. To add cookies in our website in terminal enter the following command: `npm i jsonwebtoken`  
  54. update `controller>auth.js`
  ```
import jwt from "jsonwebtoken";
  export const login = async (req, res, next) => {
    try {
        const user = await User.findOne({username:req.body.username})
        if(!user) return next(createError(404, "User not found!"));
        
        const isPasswordCorrect = await bcrypt.compare(req.body.password, user.password);
        if(!isPasswordCorrect) return next(createError(400, "Wrong Password or Username!"));
        
        const token = jwt.sign({id:user._id, isAdmin: user.isAdmin}, "secretKey")

        const { password, isAdmin, ...otherDetails } = user._doc;
        res.status(200).json({...otherDetails});
    } catch (err) {
        next(err)
    }
}
  ```
  55. run the following command in terminal to generate secret key `openssl rand -base64 32` and copy the secrey key from it   
  . if openssl is not recognize in your pc refer this: https://medium.com/swlh/installing-openssl-on-windows-10-and-updating-path-80992e26f6a1  
  . through this install Win64 OpenSSL v1.1.1q MSI in https://slproweb.com/products/Win32OpenSSL.html  
  . then in environment variable set the path (C:\Program Files\OpenSSL-Win64\bin)  
  56. Not paste the secret key from terminal to `.env` file
  ```
  JWT = <secret key comes here>
  ```
  57.going back to `controller>auth.js`
  in place of `secretKey` in const token paste the following: `process.env.JWT`  
  58. Now to add our token to cookies, in terminal install `npm i cookie-parser`  
  59.In `index.js`, type the following
  ```
  import cookieParser from "cookie-parser";

//middlewares
app.use(cookieParser());
  ```
  60. `controller>auth.js`  
  instead of res.status update this
  ```
  res.cookie("access_token", token, {
            httpOnly: true,
        }).status(200).json({...otherDetails});
  ```
  61.Node.js JWT Role-Based Authorization Verification  
  . if its admin, then they will be able to delete the hotel
  62. just like `controller>hotel.js` paste the following in `controller>user.js`
  ```
  import User from "../models/Hotel.js";

export const createUser = async (req, res, next) => {
    const newUser = new User(req.body);
    try {
        const savedUser = await newUser.save();
        res.status(200).json(savedUser);
    } catch(err) {
        next(err);
    }    
}
export const updateUser = async (req, res, next) => {
    try {
        const updateUser = await User.findByIdAndUpdate(req.params.id, { $set: req.body }, { new : true});
        res.status(200).json(updateUser);
    } catch(err) {
        res.status(500).json(err);
    }   
}
export const deleteUser = async (req, res, next) => {
    try {
        await User.findByIdAndDelete(req.params.id);
        res.status(200).json("User has been deleted");
    } catch(err) {
        res.status(500).json(err);
    }
}
export const getUser = async (req, res, next) => {
    try {
        const user = await user.findById(req.params.id);
        res.status(200).json(User);
    } catch(err) {
        res.status(500).json(err);
    }   
}
export const getUsers = async (req, res, next) => {
    try {
        const users = await User.find();
        res.status(200).json(users);
    } catch(err) {
        res.status(500).json(err);
    }    
}
  ```
  63. just like `routes>hotel.js` paste the following in `routes>user.js`  
  ```
  import express from "express";
import { createUser, deleteUser, getUser, getUsers, updateUser } from "../controllers/user.js";
import Hotel from "../models/Hotel.js";

const router =  express.Router();

// Node.js MongoDB CRUD Operations
// CREATE
router.post("/", createUser);

// UPDATE
router.put("/:id", updateUser);

// DELETE
router.delete("/:id", deleteUser);

// GET
router.get("/:id", getUser);

// GET ALL
router.get("/", getUsers);

export default router;
  ```
  64. To allow update user:  
  65.  
  66.  
  67.   
  68.  
  69.  
  70.  
