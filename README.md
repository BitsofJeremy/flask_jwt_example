# Flask With JWT

A simple Flask app with JWT for authentication


### To Signup

http -f POST http://localhost:5000/signup name=test email=test@email.com password=1234

### To get JWT

http -f POST http://localhost:5000/login email=test@email.com password=1234

### To access /user route

http http://localhost:5000/user x-access-token:long_token_string



## How the encoding and decoding work

Open a Python REPL in the directory.  For this part we will just encode and decode a UUID.

Module imports

```python
import jwt
from datetime import datetime, timedelta
import uuid # for public id
```

There needs to be an app SECRET_KEY to hash up our data.

```python
SECRET_KEY = 'test'
```

Here is our data we would like to pass secretly.

```python
public_id = str(uuid.uuid4())
```

Encode our token with data claim, expiration, SECRET_KEY, and algorithm

```python
token = jwt.encode({'public_id': public_id,'exp' : datetime.utcnow() + timedelta(minutes = 30)}, SECRET_KEY, algorithm='HS256')
```

Decode the token to get the data out

```python
data = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
```


Example in the Python REPL

```python
>>> import jwt
>>> from datetime import datetime, timedelta
>>> import uuid # for public id
>>> 
>>> SECRET_KEY = 'test'
>>> public_id = str(uuid.uuid4())
>>> 
>>> public_id
'33d97101-896a-4cf6-ba80-a2cef3a6cb0d'
>>> token = jwt.encode({'public_id': public_id,'exp' : datetime.utcnow() + timedelta(minutes = 30)}, SECRET_KEY, algorithm='HS256')
>>> 
>>> token
'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJwdWJsaWNfaWQiOiIzM2Q5NzEwMS04OTZhLTRjZjYtYmE4MC1hMmNlZjNhNmNiMGQiLCJleHAiOjE2MTkzMTAzOTd9.E_zuFQs35E8P7LKrPQyQC9YtrIxSvDQn8WvvDPR367U'
>>> data = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
>>> data
{'public_id': '33d97101-896a-4cf6-ba80-a2cef3a6cb0d', 'exp': 1619310397}
>>> public_id == data['public_id']
True
>>> 
```

Using this we can create simple auth for our web apps.



### Example hitting the endpoints from the command line with HTTPie

Open a terminal and fire up the app.

```bash
source venv/bin/activate
pip install -r requirements.txt
python app.py

 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 206-073-131

```

Now open another terminal and try the following commands:

POST to create a new user

```bash
http -f POST http://localhost:5000/signup name=test email=test@email.com password=1234
```
example:

```bash

(venv) test@computer flask_jwt_example % http -f POST http://localhost:5000/signup name=test email=test@email.com password=1234
HTTP/1.0 201 CREATED
Content-Length: 24
Content-Type: text/html; charset=utf-8
Date: Sun, 25 Apr 2021 00:21:30 GMT
Server: Werkzeug/1.0.1 Python/3.9.4

Successfully registered.
```

POST a login

```bash
http -f POST http://localhost:5000/login email=test@email.com password=1234
```
example:

```bash
(venv) test@computer flask_jwt_example % http -f POST http://localhost:5000/login email=test@email.com password=1234
HTTP/1.0 201 CREATED
Content-Length: 191
Content-Type: application/json
Date: Sun, 25 Apr 2021 00:21:41 GMT
Server: Werkzeug/1.0.1 Python/3.9.4

{
    "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJwdWJsaWNfaWQiOiJiODE1OTNmMC00ZDg4LTRkNmItOWMyNC04ZmU0M2VkOWNmZjAiLCJleHAiOjE2MTkzMTE5MDF9._SSCOe0yU5msxAPqZh0q-nKJ_WXIu-dLYWRNTu3cuAM"
}
```

Copy the token between the quotes.  The GET the user endpoint

```bash
http http://localhost:5000/user x-access-token:eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJwdWJsaWNfaWQiOiJiODE1OTNmMC00ZDg4LTRkNmItOWMyNC04ZmU0M2VkOWNmZjAiLCJleHAiOjE2MTkzMTE5MDF9._SSCOe0yU5msxAPqZh0q-nKJ_WXIu-dLYWRNTu3cuAM
```

example:

```bash
(venv) test@computer flask_jwt_example % http http://localhost:5000/user x-access-token:eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJwdWJsaWNfaWQiOiJiODE1OTNmMC00ZDg4LTRkNmItOWMyNC04ZmU0M2VkOWNmZjAiLCJleHAiOjE2MTkzMTE5MDF9._SSCOe0yU5msxAPqZh0q-nKJ_WXIu-dLYWRNTu3cuAM
HTTP/1.0 200 OK
Content-Length: 160
Content-Type: application/json
Date: Sun, 25 Apr 2021 00:21:57 GMT
Server: Werkzeug/1.0.1 Python/3.9.4

{
    "users": [
        {
            "email": "test@email.com",
            "name": "test",
            "public_id": "b81593f0-4d88-4d6b-9c24-8fe43ed9cff0"
        }
    ]
}

```
