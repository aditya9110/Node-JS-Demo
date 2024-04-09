# Node-JS-Demo

## Invalid Route
```js
router.all("*", (req, res) => {
  res.status(404).json({ "message": "Invalid path request" });
})
```

## Errorlogger.js
```js
let errorLogger function (err, req, res, next) {
  const logMessage = "" + res.statusCode + " " + err + "\n";
  fs.appendFile('errorLogger.txt', logMessage, (err) => {
    if (err) return next(err);
  });
  next();
}
```

## Validators.js
```js
validator.validatePassword = (password) => {
  var re = /^(?=.*\d)(?=.*[!@#$%^&*])(?=.*[a-z])(?=.*[A-Z]).{8,}$/;
  if (re.test(password)) {
    return true
  }
  else {
    let err = new Error("Password is invalid");
    err.status = 400;
    throw err;
  }
}

validator.validatePhoneNo = (phoneNo) => {
  if (phoneNo.toString().length == 10) {
    return true
  }
  else {
    let err = new Error("Phone number is invalid");
    err.status = 400;
    throw err;
  }
}

validator.validateEmailId = (emailId) => {
  if (emailId.includes('@') && emailId.includes('.com')) {
    return true
  }
  else {
    let err = new Error("Email Id is invalid");
    err.status = 400;
    throw err;
  }
}

validator.validateShiftType (shiftType) => {
  if (shiftType.toLowerCase() == 'house' || shiftType.toLowerCase() == 'vehicle') {
    return true
  }
  else {
    let err = new Error("Shifting type is invalid");
    err.status = 400;
    throw err;
  }
}

validator.validateName = (name) => {
  if (name.length >= 5) {
    return true
  }
  else {
    let err = new Error("Name is invalid");
    err.status = 400;
    throw err;
  }
}
```
## User.js
### Register
```js
exports.registerUser async (req, res, next) => {
  //Code here
  try {
    var name = req.body.name;
    var emailId = req.body.emailId;
    var password = req.body.password;
    var phoneNo = req.body.phoneNo;

    if (validator.validateName(name) && validator.validateEmailId(emailId) && validator.validatePassword(password) && validator.validatePhoneNo (phoneNo)) {
      let userCheck = await models.userModel.find({"emailId": emailId})
      if (userCheck.length == 0) {
        let newUser = await models.userModel.create(req.body);
        if (newUser!= null) {
          res.status(201).json({"message": "User registered successfully!!"})
        }
        else {
          let err = new Error("Registration Failed. Please try again...");
          err.status = 400;
          throw(err);
        }
      }
      else {
        let err = new Error("User already exists!");
        err.status = 409;
        throw(err);
      }
    }
  } catch (err) {
    res.status(err.status).send({"message": err.message})
    next (err)
  }
}
```

### Login
```js
exports.loginUser = async (req, res, next) => {
  //Code here
  try {
    let checkUser = await models.userModel.find({"emailId": req.body.emailId});
    console.log(checkUser);
    if (checkUser.length > 0) {
      res.json({"message": "User login successful"})
    }
    else {
      let err = new Error("Please register yourself to avail the services");
      err.status = 401;
      throw(err);
    }
  } catch (err) {
    res.status(err.status).send({"message": err.message})
    next (err)
  }
}
```

### View Packages
```js
exports.viewPackages = async (req, res, next) => {
  //Code here
  try {
    let packages = await models.packageModel.find({}, {"_id": 0, "__v": 0});
    console.log(packages);
    if (packages) {
      res.send(packages);
    }
    else {
      let err = new Error("Unable to load Package details!! Please try again...");
      err.status = 400;
      throw(err);
    }
  } catch (err) {
    res.status(err.status).send({"message": err.message})
    next (err)
  }
}
```

### Book Slot
```js
exports.bookSlot async (req, res, next) => {
  //Code here
  try {
    shiftFrom = req.body.bookings.shiftFrom
    shiftTo = req.body.bookings.shiftTo
    shiftType = req.body.bookings.shiftType

    let checkUser = await models.userModel.find({"emailId": req.body.emailId});
    console.log(checkUser);
    if (checkUser.length > 0 && validator.validateShiftType(shiftType)) {
      let booking = await models.userModel.findOneAndUpdate({"emailId": req.body.emailId}, {"bookings.shiftFrom": shiftFrom, "bookings.shiftTo": shiftTo, "bookings.shiftType": shiftType}, {new: true})
      console.log(booking);
      if (booking) {
        res.json({"message": "Booking successful"})
      }
      else {
        let err = new Error("No such user. Please check your credentials");
        err.status = 401;
        throw(err);
      }
    }
    else {
      let err = new Error("No such user. Please check your credentials");
      err.status = 401;
      throw(err);
    }
  } catch (err) {
    res.status(err.status).send({"message": err.message})
    next (err)
  }
}
```

### Cancel Booking
```js

```

### Delete User
```js
exports.deleteUser = async (req, res, next) {
//Code here
  try {
    const emailId = req.params.emailId;
    let checkUser = await models.userModel.find({ "emailId": emailId });
    console.log(checkUser);
    if (checkUser.length > 0) {
      let deletedUser = await models.userModel.deleteOne({ "emailId": emailId });
      console.log(deletedUser)
      if (deletedUser) {
        res.json({ "message": "User is removed" })
      }
      else {
        let err = new Error("Unable to delete the user. Please try again...");
        err.status = 400;
        throw (err);
      }
    }
    else {
      let err = new Error("No such user. Please check your credentials");
      err.status = 401;
      throw (err);
    }
  } catch (err) {
    res.status(err.status).send({ "message": err.message })
    next(err)
  }
}
```

## Postman Collection
```json
{
  "client": "Thunder Client",
  "collectionName": "Mock",
  "dateExported": "2024-04-09T18:15:08.4742",
  "version": "1.1",
  "folders": [],
  "requests": [
    {
      "_id": "cf4dbc3a-4a52-47e3-80c8-b1955dea596d",
      "containerId": "",
      "colId": "8b85b3c1-cc8e-4dcc-930f-15cab0956d7a",
      "name": "Setupdb",
      "url": "http://localhost:3000/setupdb",
      "method": "GET",
      "sortNum": 10000,
      "created": "2024-04-09T18:13:57.02",
      "modified": "2024-04-09T18:13:57.620Z",
      "headers": [],
      "params": [],
      "tests": []
    },
{
"_id": "e7a55014-b764-4229-6634-8ee2fa7e7147",
"colId": "8b85b3c1-cc8e-4dcc-930f-15cab0956d7a",
"containerId":"",
"name": "Register",
"url": "http://localhost:3000/users",
"method": "POST",
"sortNum": 20000,
"created": "2024-04-09T18:14:12.7212", "modified": "2024-04-09T18:14:12.7212",
"headers": [],
"params": [],
"body": {
"type": "json",
"raw": "{\" \"name\": \"Aditya\",\" \"emailId\": \"adil@gmail.com\",\" \"password\": \"Adi@1234\",\n \"phoneNo\": 9876543210\n}",
"form": []
},
"tests": []
},
{
"_id":"ae700193-3760-45be-86df-e952004ad6fe",
"colId": "@m@5b3c1-cc8e-4dcc-930f-15cab0956d7a",
"containerId":"",
"url": "http://localhost:3000/bookings",
"name": "Create Booking".
"method": "PUT",
"sortbum: 30000,
"created": "2024-04-09T18:14:20.5862",
"modified": "2024-04-09718:14:20.5862",
"headers": [],
"params": [],
"body": {
"type": "json",
"raw": "{\n \"emailId\": \"aditya@gmail.com\", \"bookings\": {\n \"shiftFrom\": \"Kalyan\",\n \"shiftTo\": \"Mumbai\",\n \"shiftType\": \"House\"\n }\n}"
"form": []
},
"tests": []
},
{
"_id": "3494ac66-0594-488c-a3eb-12eeb48cc0bf",
"colId": "8b05b3c1-ccle-4dcc-9307-15cab0956d7a",
"containerId":"",
"name": "Login",
"url": "http://localhost:3000/login",
"method": "POST",
"sortNum" 40000,
"created": "2024-04-09T18:14:30.6152",
"modified": "2024-04-09710:14:30.6152",
"headers": [],
"params": [],
"body": {
"type": "json",
"raw":"{\n \"emailId\": \"aditya@gmail.com\", \"password\": \"Adi@1234\"\n}",
"form":[]
},
"tests":[]
},
{
```
