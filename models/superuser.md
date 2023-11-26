[Back to Models](/models)

# SuperUser
The SuperUser model represents a user with elevated privileges. SuperUsers have access to administrative features that are not available to regular users.
```javascript
{
    _id: ObjectId,
    token: string,
    username: string,
    password: string,
    password_salt: string,
    join_date: datetime,
    login_date: datetime,
}
```


------
## Fields
### `SuperUser._id`
* Type: ObjectId
* Description: The unique identifier for the SuperUser.


### `SuperUser.token`
* Type: string
* Length: 32 characters
* Description: A unique authentication token for the SuperUser.

### `SuperUser.username`
* Type: string
* Minimum Length: 6 characters
* Maximum Length: 32 characters
* Description: The username for the SuperUser.

### `SuperUser.password`
* Type: string
* Length: 32 characters
* Description: The hashed password for the SuperUser.

### `SuperUser.password_salt`
* Type: string
* Length: 12 characters
* Description: A randomly generated salt used to hash the password.

### `SuperUser.join_date`
* Type: datetime
* Description: The date and time when the SuperUser account was created.

### `SuperUser.login_date`
* Type: datetime
* Description: The date and time when the SuperUser last logged in.

------

### Methods

### `SuperUser.__init__()`

### `SuperUser.new()`

### `SuperUser.login()`

### `SuperUser.check_login()`

### `SuperUser.logout()`
