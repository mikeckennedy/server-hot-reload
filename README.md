# Server Hot Reload

Include this script in your web projects for dev-time auto reloading 
of web browser when any change is detected in content. Works across
all web technologies, built out on a FastAPI / Tailwind project.

## Install and setup

Just download and include the script at the top of your web:

```html
<script tal:condition="dev_mode" src="/static/js/server-hot-reload.js"></script>
```
The script polls the current URL as you navigate around. 
It will compare the exact contents of the returned HTML. 
If it changes in any way, the script will instantly reload the
page. Be sure to include it only in your dev versions. 
Notice we're testing `tal:condition="dev_mode"` (Chameleon) in
our example.

## Configure polling interval

To change the polling interval (default of once per sec), just
edit the section:

```javascript
let checkIntervalInMs = 1000;
```
And set it to whatever makes sense for you. Longer intervals
might make sense on very slow pages.

## Pausing the script

Usually you just want this thing to constantly check the dev server. 
But there are times when it's just noise. For example, if you're
debugging the web app and have a break point you're stepping
through, the extra requests may interfere. 

To pause the script (until the page is reloaded), just open
the console in the web browser dev tools, and type 
`toggleServerReload()`.

## Reloading on JS, CSS, image resource changes

Part of the magic of this script is that it *can* reload when
you make changes to any static resources used in the page. 
But this requires the smallest amount of work on your part.

My current use-case is this working with Tailwind for the design.
For reloading, that looks like:

1. Edit the source CSS file
2. Tailwind watcher generates a built CSS file
3. Built CSS file is included the Python web HTML template
4. Template appends a hash ID for the state of the CSS file
5. Changes to the source CSS thus trigger a change in the final ID
6. New ID means the page contents change and the script does a reload

So in that way, even editing an input file to tailwind will 
auto refresh all browsers pointed at the page during development.

To make this happen, you need to append some sort of ID to
your static resources (this is great for stale content busting 
in general). In Python, [I use this bit of code](https://github.com/talkpython/fast-python-webapps-with-cdns/blob/main/code/ch8_perf_testing/infrastructure/cache_buster.py).

However you do it, the static URLs should look something like this, 
(notice the **?v=** hash ID):

```html
<link href="/static/site.css?v=670022" rel="stylesheet">

<img src="/static/img/logo.webp?v=a4c931" />
```





