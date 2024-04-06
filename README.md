# Node-JS-Demo

## Validators.js
```js
validator.validatePassword = (password) => {
  var re = /^(?=.*\d)(?=.*[!@#$%^&*])(?=.*[a-z])(?=.*[A-Z]).{8,}$/;
  if (re.test(password)) {
    return true

  else {
    let err = new Error("Password is invalid");
    err.status = 400;
    throw err;
  }
}

validator.validatePhoneNo = (phoneNo) => {
  if (phoneNo.toString().length == 10) {
    return true

  else {
    let err = new Error("Phone number is invalid");
    err.status = 400;
    throw err;
  }
}

validator.validateEmailId = (emailId) => {
  if (emailId.includes('@') && emailId.includes('.com')) {
    return true

  else {
    let err = new Error("Email Id is invalid");
    err.status = 400;
    throw err;
  }
}

validator.validateShiftType (shiftType) => {
  if (shiftType.toLowerCase() == 'house' || shiftType.toLowerCase() == 'vehicle') {
    return true

  else {
    let err = new Error("Shifting type is invalid");
    err.status = 400;
    throw err;
  }
}

validator.validateName = (name) =>
  if (name.length >= 5) {
    return true

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
