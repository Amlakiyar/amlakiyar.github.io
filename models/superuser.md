[Back to Models](/models)

# SuperUser Model
```
{
    _id,
    token,
    username,
    password,
    password_salt,
    join_date,
    login_date
}
```

### SuperUser._id 
```
type = bson.objectid.ObjectId
```
### SuperUser.token
```
type = string
length = 32
```

### SuperUser.username
```
type = string
minimum-length = 6
maximum-length = 32
```

### SuperUser.password
```
type = string
length = 32
```

### SuperUser.password_salt
```
type = string
length = 12
```

### SuperUser.join_date
```python
type = datetime.datetime
```
### SuperUser.login_date
```
type = datetime.datetime
```