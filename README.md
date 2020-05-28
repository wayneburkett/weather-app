# What is this?

It's already midnight and I just made coffee. I'm going to make the project [described in this tutorial](https://levelup.gitconnected.com/how-to-make-a-chrome-extension-with-react-129cdcbf1414) and write down my thoughts as I go.

## What prior experience do I have?

I know JavaScript pretty well and I've made Chrome extensions before, but I've been doing mostly native mobile development for the last few years and I'm new to React.

## What do I need to get started?

First I need an API key from [OpenWeatherMap](OpenWeatherMap). Apparently I already have an account from who knows when. My key:

    be2f0221009efbbe09a49c10cc563bff

I'm going to revoke this key once I upload the project, so if you're following along at home you'll need to go get your own.

## OK, let's go!

Now we can create the project using `create-react-app`. Side note: TIL `create-react-app` will create a git repo for you if you're not initializing your project in a folder that already has one. Before now I think I'd always created the repo before I created the app.

I'm going to install everything the tutorial wants me to. I normally wouldn't install all this stuff in a new app, but this is a good learning opportunity. Here's what it says to install:

    npm i axios bootstrap formik mobx mobx-react react-bootstrap yup

Some of these are new to me. Let's see what I know about each of them:

* **axios** - promise-based ajax library (I've used this)
* **bootstrap/react-bootstrap** - I'm guessing the latter provides react components for doing what bootstrap does for non-react apps
* **formik** - forms (I've seen this in some previous tutorials)
* **mobx/mobx-react** - I only know this is a state manager because I read ahead :)
* **yup** - yup, I have no idea

OK, so yup is for form validation. Nothing too crazy here.

Now I'm just copying everything into the JS files it says to create and trying to follow along with the React code. React is pretty incredible in that it doesn't take long before you start thinking in terms of components. It's a powerful model. It seems like it's really easy for an experienced JavaScript developer to get rolling with it.

OK, so I created all the files, ran `npm run build`, loaded the unpacked extension, and...it failed with a CSP error, as [predicted by one of the comments on the tutorial](https://medium.com/@benfargher/hi-thanks-for-this-im-getting-the-following-error-however-and-the-popup-fails-to-load-4c703da1a8ff). This is because the build command inlines some JS in `index.html` and I'm guessing there's something wrong with the `content_security_policy` in `manifest.json`. Perhaps the inlined JS has changed since the tutorial was written (which would invalidate the hash)?

In fact, it looks like Chrome will tell you in the error what the hash should be:

> Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' 'sha256-iKzjkrxmbwS3kHKHpCyzu0KtM+aeGMeR7zVi2tvRmoA0'". Either the 'unsafe-inline' keyword, a hash ('sha256-iKzjkrxmbwS3kHKHpCyzu0KtM+aeGMeR7zVi2tvRmoA='), or a nonce ('nonce-...') is required to enable inline execution.

So the `content_security_policy` should look like this:

    "content_security_policy": "script-src 'self' 'sha256-iKzjkrxmbwS3kHKHpCyzu0KtM+aeGMeR7zVi2tvRmoA='; object-src 'self'",

And this does indeed work. However, I think it would be better to get rid of that inlined script entirely. And it turns out there's an environment variable to do just that: `INLINE_RUNTIME_CHUNK`.

So I tried modifying the build command in `manifest.json` like this:

    set INLINE_RUNTIME_CHUNK=false&&react-scripts build

Note that the lack of spaces between commands there is intentional, based on [an earlier bug report](https://github.com/facebook/create-react-app/issues/8825). But the script was still inlined after a re-build (with or without any such spaces):

    [~/src/weather-app] (master) $ cat build/index.html
    <!doctype html><html lang="en"><head><meta charset="utf-8"/><link rel="icon" href="/favicon.ico"/><meta name="viewport" content="width=device-width,initial-scale=1"/><meta name="theme-color" content="#000000"/><meta name="description" content="Web site created using create-react-app"/><link rel="apple-touch-icon" href="/logo192.png"/><link rel="manifest" href="/manifest.json"/><title>React App</title><link href="/static/css/main.0eb0d8d2.chunk.css" rel="stylesheet"></head><body><noscript>You need to enable JavaScript to run this app.</noscript><div id="root"></div><script>!function(e){function r(r){for(var n,a,p=r[0],l=r[1],f=r[2],c=0,s=[];c<p.length;c++)a=p[c],Object.prototype.hasOwnProperty.call(o,a)&&o[a]&&s.push(o[a][0]),o[a]=0;for(n in l)Object.prototype.hasOwnProperty.call(l,n)&&(e[n]=l[n]);for(i&&i(r);s.length;)s.shift()();return u.push.apply(u,f||[]),t()}function t(){for(var e,r=0;r<u.length;r++){for(var t=u[r],n=!0,p=1;p<t.length;p++){var l=t[p];0!==o[l]&&(n=!1)}n&&(u.splice(r--,1),e=a(a.s=t[0]))}return e}var n={},o={1:0},u=[];function a(r){if(n[r])return n[r].exports;var t=n[r]={i:r,l:!1,exports:{}};return e[r].call(t.exports,t,t.exports,a),t.l=!0,t.exports}a.m=e,a.c=n,a.d=function(e,r,t){a.o(e,r)||Object.defineProperty(e,r,{enumerable:!0,get:t})},a.r=function(e){"undefined"!=typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},a.t=function(e,r){if(1&r&&(e=a(e)),8&r)return e;if(4&r&&"object"==typeof e&&e&&e.__esModule)return e;var t=Object.create(null);if(a.r(t),Object.defineProperty(t,"default",{enumerable:!0,value:e}),2&r&&"string"!=typeof e)for(var n in e)a.d(t,n,function(r){return e[r]}.bind(null,n));return t},a.n=function(e){var r=e&&e.__esModule?function(){return e.default}:function(){return e};return a.d(r,"a",r),r},a.o=function(e,r){return Object.prototype.hasOwnProperty.call(e,r)},a.p="/";var p=this["webpackJsonpweather-app"]=this["webpackJsonpweather-app"]||[],l=p.push.bind(p);p.push=r,p=p.slice();for(var f=0;f<p.length;f++)r(p[f]);var i=l;t()}([])</script><script src="/static/js/2.b1444209.chunk.js"></script><script src="/static/js/main.4ab6317a.chunk.js"></script></body></html>%

So I added it to `.env` in the project's root:

    INLINE_RUNTIME_CHUNK=false

And it worked! This is what you want to see (no inlined JavaScript):

    [~/src/weather-app] (master) $ cat build/index.html
    <!doctype html><html lang="en"><head><meta charset="utf-8"/><link rel="icon" href="/favicon.ico"/><meta name="viewport" content="width=device-width,initial-scale=1"/><meta name="theme-color" content="#000000"/><meta name="description" content="Web site created using create-react-app"/><link rel="apple-touch-icon" href="/logo192.png"/><link rel="manifest" href="/manifest.json"/><title>React App</title><link href="/static/css/main.0eb0d8d2.chunk.css" rel="stylesheet"></head><body><noscript>You need to enable JavaScript to run this app.</noscript><div id="root"></div><script src="/static/js/runtime-main.a86148e1.js"></script><script src="/static/js/2.b1444209.chunk.js"></script><script src="/static/js/main.4ab6317a.chunk.js"></script></body></html>

Now you don't need `content_security_policy` at all; you can remove it from the manifest.

But the app still doesn't quite work. 

First of all, it looks pretty bad. The styles are not working. Second, none of the functionality works and there are 404 errors. So the first thing I'm going to check is to see if the requests in `requests.js` are still valid. And if so, then perhaps I've incorrectly structured the project (i.e. maybe something's not properly imported). Actually, perhaps I should check that first.

OK, I've re-read the tutorial and everything seems OK with the structure (just based on what the tutorial said to do). And both requests work (remember: use your own API key here):

* http://api.openweathermap.org//data/2.5/weather?q=providence&appid=be2f0221009efbbe09a49c10cc563bff
* http://api.openweathermap.org//data/2.5/forecast?q=providence&appid=be2f0221009efbbe09a49c10cc563bff

Oh wait, maybe it actually *is* loading the data. I think maybe it didn't like my initial search term, which included a comma separating city and state (and the output on the screen isn't quite right because of the lack of styles, which should be showing/hiding tabs based on user actions). If I just put, say, "*Providence*" in the search box then I get data.

But the styles still aren't applied. Let's see what we can do about that. It looks like all that's missing is an import for the bootstrap styles. I [added this to `index.js`](https://stackoverflow.com/a/52012124/592746):

    import 'bootstrap/dist/css/bootstrap.min.css';

Now it all works!

## What's next?

* Figure out how everything else in this thing works. 
* Maybe try removing the *mobx* dependency (could be a good opportunity to try implementing it in e.g. Redux or maybe with `useReducer` hooks)?
