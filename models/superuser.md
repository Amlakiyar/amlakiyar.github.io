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

## - Fields
#### `SuperUser._id`
* Type: ObjectId
* Description: The unique identifier for the SuperUser.


#### `SuperUser.token`
* Type: string
* Length: 32 characters
* Description: A unique authentication token for the SuperUser.

#### `SuperUser.username`
* Type: string
* Minimum Length: 6 characters
* Maximum Length: 32 characters
* Description: The username for the SuperUser.

#### `SuperUser.password`
* Type: string
* Length: 32 characters
* Description: The hashed password for the SuperUser.

#### `SuperUser.password_salt`
* Type: string
* Length: 12 characters
* Description: A randomly generated salt used to hash the password.

#### `SuperUser.join_date`
* Type: datetime
* Description: The date and time when the SuperUser account was created.

#### `SuperUser.login_date`
* Type: datetime
* Description: The date and time when the SuperUser last logged in.

## - Methods

#### `SuperUser.__init__()`
```python
# Initialize the SuperUser object
def __init__(self, _id=None, username=None):
    """
    Initialize the SuperUser object with the provided credentials or by loading data from the database.

    Args:
        _id (ObjectId, optional): The unique identifier of the SuperUser to load from the database. Defaults to None.
        username (str, optional): The username of the SuperUser to load from the database. Defaults to None.
    """

    # Initialize the flag indicating if the SuperUser object is loaded from the database
    self.is_loaded = False

    # Check if an _id is provided
    if _id is not None:
        # Search for the SuperUser with the given _id in the database
        search_superuser = superusers_collection.find_one({"_id": _id})

        # If the SuperUser is found, load its data into the current object
        if search_superuser is not None:
            self._id = search_superuser["_id"]
            self.token = search_superuser["token"]
            self.username = search_superuser["username"]
            self.password = search_superuser["password"]
            self.password_salt = search_superuser["password_salt"]
            self.join_date = search_superuser["join_date"]
            self.login_date = search_superuser["login_date"]

            # Set the object as loaded
            self.is_loaded = True

    # Check if a username is provided
    elif username is not None:
        # Search for the SuperUser with the given username in the database
        search_superuser = superusers_collection.find_one({"username": username})

        # If the SuperUser is found, load its data into the current object
        if search_superuser is not None:
            self._id = search_superuser["_id"]
            self.token = search_superuser["token"]
            self.username = search_superuser["username"]
            self.password = search_superuser["password"]
            self.password_salt = search_superuser["password_salt"]
            self.join_date = search_superuser["join_date"]
            self.login_date = search_superuser["login_date"]

            # Set the object as loaded
            self.is_loaded = True
```

#### `SuperUser.new()`

```python
# Create a new SuperUser
def new(self, username, password):
    """
    Create a new SuperUser account with the specified username and password.

    Args:
        username (str): The desired username for the new SuperUser.
        password (str): The desired password for the new SuperUser.

    Returns:
        tuple: A tuple containing a status code, a message, and the newly created SuperUser object (if successful).
            - 0, "New SuperUser created": Creation successful
            - 1001, "Username already exists": Username already in use
    """

    # Check if a SuperUser with the given username already exists
    existing_superuser = superusers_collection.find_one({"username": username})
    if existing_superuser is not None:
        return 1001, "Username already exists"

    # Generate a unique token and password salt
    current_time = datetime.now()
    password_salt = uuid4().hex[:16]

    # Create a new SuperUser document with the provided credentials
    new_superuser = {
        "_id": ObjectId(),
        "token": uuid4().hex,
        "username": username,
        "password": sha256((password + password_salt).encode("utf-8")).hexdigest(),
        "password_salt": password_salt,
        "join_date": current_time,
        "login_date": None
    }

    # Insert the new SuperUser document into the database
    superusers_collection.insert_one(new_superuser)

    # Update the current SuperUser object with the new data
    self._id = new_superuser["_id"]
    self.token = new_superuser["token"]
    self.username = new_superuser["username"]
    self.password = new_superuser["password"]
    self.password_salt = new_superuser["password_salt"]
    self.join_date = new_superuser["join_date"]
    self.login_date = new_superuser["login_date"]

    # Set the SuperUser object as loaded
    self.is_loaded = True

    # Return a success message and the newly created SuperUser object
    return 0, "New SuperUser created", self
```

#### `SuperUser.login()`

```python
# SuperUser Login
def login(self, password):
    """
    Check if the provided password matches the stored password for the current SuperUser.
    If the password is correct, update the user's login date and generate a new login token.

    Args:
        password (str): The password to be checked.

    Returns:
        tuple: A tuple containing a status code and a message.
            - 0: Login successful
            - 1001: SuperUser is not loaded yet
            - 1002: Password is not correct
    """

    # Check if the SuperUser object is loaded from the database
    if not self.is_loaded:
        return 1001, "SuperUser is not loaded yet"

    # Hash the provided password with the user's salt
    hashed_password = sha256((password + self.password_salt).encode("utf-8")).hexdigest()

    # Check if the hashed passwords match
    if hashed_password != self.password:
        return 1002, "Password is not correct"

    # Update the user's login date and generate a new login token
    now_datetime = datetime.now()
    new_token = uuid4().hex

    # Update the user's token in the database
    superusers_collection.update_one({"_id": self._id}, {"$set": {"token": new_token, "login_date": now_datetime}})

    # Update the current SuperUser object's token and login date
    self.token = new_token
    self.login_date = now_datetime

    # Return a success message
    return 0, "Login was successful"
```

#### `SuperUser.check_login()`

```python
# SuperUser Login Check
def check_login(self, token):
    """
    Verify if the provided token matches the current SuperUser's token.

    Args:
        token (str): The token to be validated.

    Returns:
        tuple: A tuple containing a status code and a message.
            - 0, "Token is valid": Token is valid and associated with the current SuperUser.
            - 1001: SuperUser is not loaded yet
            - 1002, "Token is not valid": Token is invalid or does not belong to the current SuperUser.
    """

    if not self.is_loaded:
        return 1001, "SuperUser is not loaded yet"

    # Check if the provided token matches the current SuperUser's token
    if token != self.token:
        return 1002, "Token is not valid"

    else:
        return 0, "Token is valid"
```

#### `SuperUser.logout()`

```python
# Logout SuperUser
def logout(self):
    """
    Invalidate the current SuperUser's login token, effectively logging them out.

    Returns:
        tuple: A tuple containing a status code and a message.
            - 0, "Logout was successful": Logout successful.
            - 1001: SuperUser is not loaded yet
    """

    if not self.is_loaded:
        return 1001, "SuperUser is not loaded yet"

    # Generate a new token to invalidate the current one
    new_token = uuid4().hex

    # Update the SuperUser's token in the database to the newly generated one
    superusers_collection.update_one({"_id": self._id}, {"$set": {"token": new_token}})

    # Update the current SuperUser object's token with the newly generated one
    self.token = new_token

    return 0, "Logout was successful"
```