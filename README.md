# Django-Realtime: an iShout.js client for Django.

iShout.js is a node.js + socket.io server used for adding real-time notifications to your existing web application.
This project adds easy iShout.js integration for Django, thus enabling you to easily
add real-time notifications to you exiting Django applications.

Before installing and using the client, make sure you setup iShout.js using [this guide](https://bitbucket.org/inzane/ishout "iShout.js Installation").


## Installation

* install the client either by cloning this repo, or, if using pip, by using the following command:

    pip install django-realtime

* add `drealtime` to `INSTALLED_APPS` in your settings.py file.


## Configuration

* add `'drealtime.middleware.iShoutCookieMiddleware'` to your `MIDDLEWARE_CLASSES`settings. right above `SessionMiddleware`. this will take care of setting the authentication cookie.
* in your templates, add `{% load drealtimetags %}` at the top of the template. inside your `<head>` tag, add `{% ishout_js %}`. this will include the proper `<script>` tags for you. place this tag above any js files that use the ishout.js client.
* the following optional settings are availabe:
    * `ISHOUT_CLIENT_ADDR` - defaults to `'localhost:5500'`. *should probably be changed for production.*
    * `ISHOUT_API_ADDR` - defaults to `'localhost:6600'`.
    * `ISHOUT_HTTPS` - defaults to `False`. whether or not iShout.js is setup to use SSL.


## Usage

Now, from a view, emit a message to a user:

```python
from drealtime import iShoutClient
ishout_client = iShoutClient()

# This is our pseudo view code
def comment_on_blog(request):
    post = Post.objects.get(...)
    # Process stuff, handle forms, do whatever you want.
    ishout_client.emit(
        post.author.pk,
        'notifications',
        data={ 'msg' : 'You have a new comment!' }
    )
    # Some other things happening here..
    return render_to_response('blog_post.html', ctx)
```

In your HTML code, add the following JS snippet, to receive this notification on the client's side:

```js
// use .on() to add a listener. you can add as many listeners as you want.
ishout.on('notifications', function(data){
    alert(data.msg);
});

// .init() will authenticate against the ishout.js server, and open the WebSocket connection.
ishout.init();
```

Your client should receive an alert with the notification. the channel name (`notifications`) and the data you send
are completley up to you, so be creative and use it wisely.

If you have more than one channel we want to listen on, we can also chain these calls:

```js
ishout.on('notifications', function(data){
    popupNotification(data.msg);

}).on('alerts', function(data)}{
    alert(data.alert_msg);

}).on('something-else', function(data){
    $('#someDiv').html(data.htmlContent);
    
}).init();
```

## License 

(The MIT License)

Copyright (c) 2012 Anish menon anish@inzane.in;

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
