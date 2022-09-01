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
> import express from "express";  
>  
> const app = express();  
>   
> app.listen(8000, ()=>{  
>    console.log("server running");  
> });  

15. Now let's setup our mongoDB database. Go to https://cloud.mongodb.com/
16. On top left, (Look at the below image,for reference - highlighted in red)  
<ul>  
  <li>Open the dropdown</li>  
  <li>Give a name to your database</li>  
  <li>Press the `new project` button</li>  
</ul>  
 ![step16](https://user-images.githubusercontent.com/69050759/187870600-6e88ff48-2a73-45d2-bfa6-28f24d5d8ff2.png)  
 17. 
