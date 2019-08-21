## Running

To run the app:

```
virtualenv .env
source .env/bin/activate
pip install -r requirements.txt
python main.py
```

## REST API

* [Create a session](#create-a-session) : `POST /session`
* [Delete the session](#delete-the-session) : `DELETE /session/:id`
* [Save data to the session](#save-data-to-the-session) : `POST /session/:id`
* [Read BPM](#read-bpm) : `GET /session/:id/bpm/:timestamp`


## Create a session

Create a session with unique session id and return the id. The device identifier is just for logging, has no impact on behavior.

**URL** : `/session`

**Method** : `POST`

**Data constraints**

```json
{
    "device_identifier": "[string with length of 64 max]"
}
```

**Data example** All fields must be sent.

```json
{
    "device_identifier": "048f2e2c-c3ef-11e9-aa8c-2a2ae2dbcce4"
}
```

### Success Response 

**Condition** : If everything is OK and the session is created.

**Code** : `200 OK`

**Content example**

```json
{
    "session_id": 1234,
    "created": 1566376847000,
    "device_identifier": "048f2e2c-c3ef-11e9-aa8c-2a2ae2dbcce4"
}
```

`session_id` is a 4-digit positive integer. `created` is a Unix Timestamp in milliseconds of the session creation.


## Delete the session

Delete the session and all data binded to it. For the sake of security, you need to include the secret key to the request.

**URL** : `/session/:id`

**Method** : `DELETE`

**Data constraints**

```json
{
    "secret": "[integer, must be 42]"
}
```

**Data example** All fields must be sent.

```json
{
    "secret": 42
}
```

### Success Response 

**Condition** : If everything is OK and the session is deleted.

**Code** : `200 OK`

**Content example**

```json
{
    "entities_deleted": 14
}
```


## Save data to the session

Introduces new data to the session. For now, the server only processes the `bpm` fields of the request

**URL** : `/session/:id`

**Method** : `POST`

**Data constraints**

```json
{
    "data": [
        {
            "timestamp": "[integer, Unix timestamp in milliseconds]",
            "bpm": "[integer, must be 0-255]"
        },
    ]
}
```

**Data example**

```json
{
    "data": [
        {
            "timestamp": 1566376847000,
            "bpm": 77
        },
        {
            "timestamp": 1566376845000,
            "bpm": 78
        }
    ]
}
```

### Success Response 

**Condition** : If everything is OK and the data has been added.

**Code** : `200 OK`

**Content example**

```json
{
    "entities_added": 2
}
```

## Read BPM

Read the BPM data in the time span `timestamp - now`. Setting `timestamp` to 0 requests all data.

**URL** : `/session/:id/bpm/:timestamp`

**Method** : `GET`

**Data example** : empty

### Success Response

**Condition** : If everything is OK and the data has been gathered.

**Code** : `200 OK`

**Content example**

```json
{
    "data": [
        {
            "timestamp": 1566376847000,
            "bpm": 77
        },
        {
            "timestamp": 1566376845000,
            "bpm": 78
        }
    ]
}
```

