Switch metric: On Display
---------------------------------
This handler called when tile needs to be updated. E.g. when last activity time needs to be updated or new payload received and needs to be displayed.
>`Note`: This handler called only for visible tiles. If tile scrolled away, this handler will not be called.
`Important`:  this handler is executed each time in `CLEAN` mode. This means, that previous call state will be cleaned up (any declared variables and functions from previous call will be purged in subsequent call)!
To pass data between calls and handlers use `event.data`.

### In this handler you can use the following:

####**`app.publish(topic, payload, retained, qos);`**

Publish `payload` (String) to `topic` (String) with specified `retained` flag (Boolean) and `qos` (Integer). Example:

```js
app.publish('messages/alert', 'HOT', false, 0);
```

---

####**`app.openUri(uri);`**

Open specified `uri` (String) in an external app. `uri` examples:
- `http://www.example.com/page.html`
- `tel:123`
- `content://contacts/people/`
>Usually you don't want to use it in `On Display` handler, because `On Display` executed every time, when tile needs to be updated and this happens quite often.

```js
app.openUri('http://www.example.com');
```
---

####**`event.data`**

(Any type) Custom runtime user data, bound to this particular metric. You can get/set any data, which you need to be preserved between calls.
The data shared between `On Receive`, `On Display`, `On Tap` handlers. So you can set data in `On Receive` and use this data later in `On Display`.
>`Warning`: never pass functions through `event.data`!
The data `NOT` preserved between app launches. Usually, because the data is not assigned by default, you want to start your handler's code with check if data assigned, and then use the data:

In `On Receive` handler:

```js
if (!event.data) {
    event.data = {};
}
event.data['some flag'] = 
	(event.payload == 'something' ? true : false);
```

In `On Display` or in `On Tap` handler:

```js
if (event.data['some flag']) {
    // do something
} else {
    // do something other
}
```

---

####**`event.getSecondsSinceLastActivity()`**

(Integer) Number of seconds, elapsed since last activity (since payload received):

```js
// blink tile to draw attention, if last payload
// was received more than 60 seconds ago
event.blink = event.getSecondsSinceLastActivity() > 60;
```

---

####**`event.lastActivityString`**

(String) Text to display at the bottom of tile. By default it contains elapsed time, but you can override it. For example:

```js
event.lastActivityString = 
	event.getSecondsSinceLastActivity() + " seconds ago";
```

---

####**`event.blink`**

(Boolean) Tile will blink if value of the property is `true`. Example:

```js
// blink tile if it is in 'off' state
event.blink = (event.getLastPayload() == 'off');
```

---

####**`event.name`**

(String) Text to display at the top of the tile. By default it contains metric name. You can override it.

---

####**`event.getLastPayload()`**

(String) Returns last received raw payload. Raw means, that prefix, postfix and any other payload transformations not applied yet (if configured).

```js
// change icon color depending on the switch state
// event.on is Boolean value which represents state of the switch
if (event.on) {
	event.iconColor = '#ffcccc';
} else {
	event.iconColor = '#ffffff';
};
```

---

####**`event.iconColor`**

(HEX String) Main icon color. E.g. `#ffffcc`.

```js
// change icon color depending on the switch state
event.iconColor = event.on ? '#ffcccc' : '#ffffff';
```