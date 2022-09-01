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
