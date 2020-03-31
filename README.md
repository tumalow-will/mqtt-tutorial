# Tumalow MQTT Tutorial
Test your Tumalow devices.

## Install requirements
MQTT is an open standard and there are many tools you can use to publish and subscribe.  Here we will use mosquitto.  

```
sudo apt-get install mosquitto mosquitto-clients
```

To make sure it installed, try
```
which mosquitto_pub
```
and make sure you get some output.


## Sending telemetry

Log into the dashboard and copy the access token to your clipboard.  Then in a terminal type.


```
export ACCESS_TOKEN=<PASTE TOKEN HERE>
echo $ACCESS_TOKEN
```

You should see your access token printed to the screen.  You're ready to publish data.

```
./publish_telemetry
```

which should give you something similar to this output.

```
Client mosqpub|19409-hephaestu sending CONNECT
Client mosqpub|19409-hephaestu received CONNACK
Client mosqpub|19409-hephaestu sending PUBLISH (d0, q0, r0, m1, 'v1/devices/me/telemetry', ... (27 bytes))
Client mosqpub|19409-hephaestu sending DISCONNECT
```

On the dashboard, click `LATEST TELEMETRY` and you should see your data.  Since there was no timestamp included, the server timestamped the data automatically upon recipt.


## Sending and receiving remote commands

The client-side should subscribe to the topic

```
v1/devices/me/rpc/request/+
```

where the `+` is the MQTT wildcard, and will be an integer request ID.  The payload of the packet can be be any json-formatted string you'd like, but I recommend the following format


```
{
   func: <string of the function you want run>,
   args: [<list of arguments to the function>],
   kwargs: { <key: value pairs of key word arguments to the function}
}
```

After performing the requested function, the client should respond back to the topic

```
v1/devices/me/rpc/response/$request_id
```

Here again your response can be any json-formatted string.  I recommend the following

```
{
    status_code: <number code>,
    resp: <string giving the response>,
    except: <optional string describing any exceptions that came up>
}

