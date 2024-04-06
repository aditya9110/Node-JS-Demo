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
