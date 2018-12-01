## Callbacks

- In JavaScript a callback function is a function which is passed into another function, the function that is passed in will later be invoked inside of the parent function creating a new routine or action to be completed. You can think of a callback as a 'trigger' for JavaScript to stop, look for other things to do then "call-back" that function when it has nothing to do again.

```js
function someFunction(string, callback) {
  console.log(string);
  callback();
}

function logWhenDone() {
  console.log('Done');
}

someFunction('Hello World', logWhenDone);
```

### When to use callbacks?

- Callback functions are useful typically when we want things to happen in a specific order. Lets say I make an http request to an API using `ajax()`, that request will take an indeterminant amount of time, what JavaScript does is it removes the callback from the callstack after `ajax()` has been fired off and places it onto a message queue once the request is resolved. When the call stack frees up the callback is moved to there where it is finally executed. Lets look at this example with and without a callback:

```js
// Without Callback

var response = makeHTTPRequest('http://www.google.com/api/v1/fake-api');

function parseReponse(response) {
  console.log(response); //underfined
  // parses response
}

parseReponse(response);
```

- As we can see the response is `undefined`, the reason is that the function had not returned by the time the variable was declared.

```js
function ajax(url, callback) {
  const xhr = new XMLHttpRequest();
  xhr.timeout = 2000;
  xhr.onreadystatechange = function(e) {
    if (xhr.readyState === 4) {
      if (xhr.status === 200) {
        callback(null, xhr.response);
      } else {
        callback(xhr.status, null);
      }
    }
  };
  xhr.ontimeout = function() {
    console.log('Timeout');
  };
  xhr.open('get', url, true);
  xhr.send();
}

ajax('http://www.google.com/api/v1/fake-api', function(err, response) {
  if (!err) {
    // parses response
  } else {
    console.log(err);
  }
});
```

- Rather then using some variable declaration which is passed into a function we can passs in a callback function directly on the function that initiates the asynchronous behavior. From there it is possible to handle the response when the callback is "called-back". While this all seems very reasonable, the example above is extremely simplified, there is no error handling, and the statement "parse response" is criminally misleading as this could potentially mean another `ajax()` call is made to fetch more data, one last thing before this horse is offically dead is that order is not guarenteed here. The reponses could be coming back in a way that is totally unexpected, this could be handled through creating gates or latches. The scenario I just described is formally known as **"callback hell"**, a situation that arises when multiple neseted callbacks are made quickly eroding readibility and maintainability, generally this is seen when it is necessary to handle many different outcomes associated with one or many callbacks.

```js
// Welcome to Callback Hell
fs.readdir(source, function(err, files) {
  if (err) {
    console.log('Error finding files: ' + err);
  } else {
    files.forEach(function(filename, fileIndex) {
      console.log(filename);
      gm(source + filename).size(function(err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err);
        } else {
          console.log(filename + ' : ' + values);
          aspect = values.width / values.height;
          widths.forEach(
            function(width, widthIndex) {
              height = Math.round(width / aspect);
              console.log(
                'resizing ' + filename + 'to ' + height + 'x' + height
              );
              this.resize(width, height).write(
                dest + 'w' + width + '_' + filename,
                function(err) {
                  if (err) console.log('Error writing file: ' + err);
                }
              );
            }.bind(this)
          );
        }
      });
    });
  }
});
```
