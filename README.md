# Node-JS-Demo

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
      let userCheck await models.userModel.find({"emailId": emailId})
      if (userCheck.length == 0) {
        let newUser await models.userModel.create(req.body);
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
        err.status = 400;
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
    let checkUser await models.userModel.find({"emailId": req.body.emailId});
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
    if (packages.length > 0) {
      res.send(packages);
    }
    else {
      let err = new Error("Unable to load Package details!! Please try again...");
      err.status = 400;
      throw(err);
    }
  } catch (err) {
    res.status(400).send({"message": "Unable to load Package details!! Please try again..."})
    next (err)
  }
}
```

### Book Slot
```js
exports.bookSlot async (req, res, next) => {
  //Code here
  try {
    shiftFrom req.body.bookings.shiftFrom
    shiftTo req.body.bookings.shiftTo
    shiftType = req.body.bookings.shiftType

    let checkUser = await models.userModel.find({"emailId": req.body.emailId});
    console.log(checkUser);
    if (checkUser.length > 0 && validator.validateShiftType(shiftType)) {
      let booking = await models.userModel.findOneAndUpdate({"emailId": req.body.emailId), {"bookings.shiftFrom": shiftFrom, "bookings.shiftTo": shiftTo, "bookings.shiftType": shiftType), (new: true))
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
    res.status(401).send({"message": "No such user. Please check your credentials"})
    next (err)
  }
}
```
