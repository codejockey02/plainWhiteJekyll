---
layout: post
title:  "Handling Image Uploads in NodeJS"
date:   2019-06-05 17:45:00 +0530
categories: Nodejs Javascript MongoDB Cloudinary
---

Nodejs is a widely used to develop REST APIs which can support the services for both web as well as Android/IOS Apps. The best thing about Nodejs is it’s Asynchronous behaviour which do not let any client stop from accessing the requested route.

This article mainly focuses on how can we upload files (which can be of any type, but I’ll be using the example of an image in this article) through Nodejs using the NPM Package Multer and then how to store the URL of that file along with other details in MongoDB Database so that it can be retrieved whenever requested. Now, let’s go step by step:

Step 1: We need to install Multer package from NPM to our directory
```javascript
npm i multer
```
Step 2: Edit the server.js file
```javascript
const express = require(‘express’);
const app = express();
const multer = require(‘multer’);
```
Import the package in your server.js or your main app.js file.

Step 3: Now we have to set up our upload directory
```javascript
const storage = multer.diskStorage({
destination: ‘uploads/’,
filename: function (req, file, cb) {
cb(null, file.fieldname + ‘-’ + Date.now());
}
});
const upload = multer({ storage: storage})
```
This code helps in giving the directory and filename to the coming file.

Step 4: Let’s make the POST Route to accept the file.
```javascript
app.post(‘/compile’,upload.single(‘avatar’), (req,res)=>{
async function upload(){
var tmp_path = req.file.path;
var file_name = req.file.originalname;
}upload();
});
```
Through this route, the middleware function upload.single(‘avatar’) accepts the file from the client in the name of “avatar”. The two functions that we are using in our asynchronous function upload() is to get the temporary file path and the file name which will be useful for our further implementation.

Now, we have our file uploaded on our server, but we cannot access that file whenever the client requests it. So, what we will do now is make use of a CDN Cloud service “Cloudinary”. It enables users to upload, store, manage, manipulate and deliver images and video for websites and apps. You would be wondering, what is CDN? Well, CDN refers to Content Delivery Network. It is a system of distributed servers (network) that deliver pages and other Web content to a user, based on the geographic locations of the user as well as the request of the user. Through Cloudinary, we can easily transform our images in whatever format we want to deliver it to the client. At first, you need to signup on Cloudinary to access your api key and cloud name. You can signup on Cloudinary <a href="http://www.cloudinary.com/users/register/free">here</a>.
You can also refer to the Nodejs documentation of Cloudinary <a href="https://cloudinary.com/documentation/node_integration">here</a> to get more ideas related to image Transformation.

Step 5: Including Cloudinary SDK support in your code.
```javascript
var cloudinary = require(‘cloudinary’);
cloudinary.config({
cloud_name: ‘your cloud name’,
api_key: ‘your api key’,
api_secret: ‘your api secret’
});
```
All these details will be given at your Cloudinary dashboard on the top left corner.

Now, let’s upload your image on Cloudinary.

Step 6: Creating schema.js file which will be containing the schema to store the URL of the uploaded file.
```javascript
const mongoose = require(‘mongoose’);
mongoose.connect(‘your_database_address’)
.then(()=> console.log(“Connected to MongoDB…”))
.catch(err => console.error(“Could Not Connect…”,err));
const image= mongoose.Schema({
url: String,
});
mongoose.Promise = global.Promise;
module.exports = mongoose.model(‘image’,image);
```

Step 7: Uploading the File to Cloudinary and it’s url to the created collection.
```javascript
cloudinary.uploader.upload( tmp_path, function(result) {
var iurl = cloudinary.url(result[“public_id”]);
var ext = “.jpg”;
var con = iurl+ext;
const newImage = new image({
url: con,
});
newImage.save()
})
},{ public_id: file_name });
```
n line 2, we can apply the transformtions if we want like “cloudinary.url(result[“public_id”],{quality:50, radius:max})”. If we do this then we’ll get the url of a circular image. We can omit the line 4, but sometimes the url doesn’t comes with proper extension, then we have to add it. If you face any problems accessing the url then make use of line 4.

If you go step by step till this, then you’ll be having the url of the uploaded image in your collection in MongoDB, now you can give this image url to any client who requests the file.

Additional Thing:

In Cloudinary, we can also upload files by changing them to Base64 String.
```javascript
cloudinary.uploader.upload(“Base64_String”,function(result){
var url = cloudinary.url(result[“public_id”]);
The same steps here…
})
```
You can also visit my <a href="https://github.com/codejockey02/multipart-trial/">GitHub Repository</a> for a proper implementation of this code.