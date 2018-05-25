## Websocket Calls Notifications

#### Contents:
* [Call Format: Websocket](/developers/apis/websocket_calls_notifications.md#call-format-websocket)
* [API Access Procedure](/developers/apis/websocket_calls_notifications.md#api-access-procedure) - Requesting Available APIs ASccess
* [Database Notifications](#database-notifications) - Available **Websocket Subscriptions**
* [Example](/developers/apis/websocket_calls_notifications.md#example---get-a-global-scubscription-callback-and-id) - Get a global scubscription callback and id

*** 

## Prerequisits

In this page example, we assumes that you have a full node running and listening to port
``8090``, locally.

>If you also want to run a wallet, please pick reasonable different
          ports and make sure you do not try to call methods at the wallet that
          are only available to the blockchain API.

## Call Format: Websocket

In Graphene, Websocket calls are stateful and accessible via regular JSON formated
websocket connection. The correct structure of the JSON call is

     {
      "id":1,
      "method":"call",
      "params":[
                0,
                "get_accounts",
                [["1.2.0"]]
               ]
     }

The parameters ``params`` have the following structure:

     [API-identifier, Method-to-Call, Call-Parameters]

In the example above, we query the ``database`` API which carries the
identifier ``0`` in our example (see more details below).

#### Example Call with `wscat`

The following will show the usage of websocket connections. We make use of the
``wscat`` application available via ``npm``:

    npm install -g wscat

A non-restricted call against a full-node would take the form:

    wscat -c ws://127.0.0.1:8090
    > {"id":1, "method":"call", "params":[0,"get_accounts",[["1.2.0"]]]}

#### Successful Calls

The API will return a properly JSON formated response carrying the same ``id``
as the request to distinguish subsequent calls.

    {
     "id":1,
     "result":  ..data..
    }

#### Errors

In case of an error, the resulting answer will carry an ``error`` attribute and
a detailed description:

    {
      "id": 0
      "error": {
        "data": {
          "code": error-code,
          "name": " .. name of exception .."
          "message": " .. message of exception ..",
          "stack": [ .. stack trace .. ],
        },
        "code": 1,
      },
    }

***

## API Access Procedure
 
The Graphene full node offers a wide range of APIs that can be accessed via
websockets. The procedure works as follows:

1. Login to the Full Node
2. Request access to an API
3. Obtain the API identifier
4. Call methods of a specific API by providing the identifier

Available APIs: 
* Database API
* Account History API
* Crypto API
* Network Broadcast API
* Network Nodes API


#### 1. Login

The first thing we need to do is to *login*::

    > {"id":2,"method":"call","params":[1,"login",["",""]]}
    < {"id":2,"result":true}

If you have ``restricted access`` then you may be required to put
your ``username`` and ``pasword`` into the quotes, accordingly. Furthermore, you
should verify, that the ``result`` give positive confirmation about your login.

#### 2. Requesting Access to an API

Most data can be queried from the ``Database API``-API to which we *register*
with the following call:::

    > {"id":2,"method":"call","params":[1,"database",[]]}

#### 3. Obtain the API identifier

After requesting access, the full node will either deny access or return an
identifier to be used in future calls::

    < {"id":2,"result":2}

The ``result`` will be our identifier for the database API, in the following
called ``DATABASE_API_ID``!

#### 4. Call methods of a specific API by providing the identifier

Now we can call any methods available to the ``database`` API via:::

    > {"id":1, "method":"call", "params":[DATABASE_API_ID,"get_accounts",[["1.2.0"]]]}

***

## Database Notifications

In Graphene, the websocket connection is used for notifications when objects
in the database change or a particular event (such as filled orders) occur.

We have the following subscriptions available:

> Every notification initiated by the full node will carry a particular ``id`` as defined by the user with the ``identifier`` parameter.

| | |
|---|---|
| `set_subscribe_callback` ( int identifier, bool clear_filter )| To simplify development a global subscription callback can be registered.|
| `set_pending_transaction_callback` (int identifier) | Notifications for incoming *unconfirmed* transactions. |
| `set_block_applied_callback` (blockid) | Gives a notification whenever the block ``blockid`` is applied to the blockchain. |
| `subscribe_to_market` (int identifier, asset_id a, asset_id b)) | Subscribes to market changes in market ``a:b`` and sends notifications with id ``identifier``.|
| `get_full_accounts` (array account_ids, bool subscribe) | Returns the full account object for the accounts in array ``account_ids`` and subscribes to changed to that account if ``subscribe`` is set to ``True``. |
| `get_objects` (vector<object_id_type> &ids) | Returns the objects corresponding to the provided IDs. If any of the provided IDs does not map to an object, a null variant is returned in its position. |




***

## Example - Get a global scubscription callback and id

- `set_subscribe_callback`

First, let's get a global scubscription callback to disctinguish our notifications from regular RPC calls:

    > {"id":4,"method":"call","params":[DATABASE_API_ID,"set_subscribe_callback",[SUBSCRIPTION_ID, true]]}

This call above will register ``SUBSCRIPTION_ID`` as id for notifications.

Now, whenever you get an object from the witness (e.g. via ``get_objects``) you will automatically subscribe to any future changes of that object.

After calling ``set_subscribe_callback`` the witness will start to send notices every time the object changes:

    > {
        "method": "notice"
        "params": [
            SUBSCRIPTION_ID, 
            [[
                { "id": "2.1.0", ...  },
                { "id": ...  },
                { "id": ...  },
                { "id": ...  }
            ]]
        ], 
    }

**Example - Full Session**

    > {"method": "call", "params": [1, "login", ["", ""]], "id": 2}
    < {"id":2,"result":true}
    
    > {"method": "call", "params": [1, "database", []], "id": 3}
    < {"id":3,"result":2}
    
    > {"method": "call", "params": [1, "history", []], "id": 4}
    < {"id":4,"result":3}
    
    > {"method": "call", "params": [2, "set_subscribe_callback", [5, false]], "id": 6}
    < {"id":6,"result":null}
    
    > {"method": "call", "params": [2, "get_objects", [["2.1.0"]]], "id": 7}
    (plenty of data coming in from this point on)

***

(ref)

- http://docs.bitshares.org/api/websocket.html
- https://github.com/bitshares/bitshares-core/wiki/Websocket-Subscriptions

