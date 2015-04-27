# Sails.js + Browserify
### What is Browserify and why would I need it?
[Browserify](https://github.com/substack/node-browserify) allows you to bring your node modules to your browser<br/>
Modules installed in Node.js using `npm` are only available  server-side. But what if you needed the same library client-side? That's where Browserify comes in.

### TL;DR
No patience? No problem! Just take a look at the `tasks` folder. Following are the files of interest:
```
/browserify.js    // this file is on root folder
/tasks/config/browserify.js
/tasks/config/syncAssets.js
/tasks/config/compileAssets.js
```

### How would I do this in Sails.js?
Getting this to work in Sails.js was bit of a hassle, so I created this seed project for anyone interested.<br/>
Let's assume we wish to browserify [Underscore.js](http://underscorejs.org/)

Here are the steps:
* Install the module you wish to browserify `npm install XYZ` or add it to your `package.json`

* Create a file `browserify.js`. Mention the modules here you wish to make available client-side
```
var underscore = require('underscore');
window.underscore = underscore;
```
* In Sails.js, all Grunt tasks are stored in /tasks folder. Add a new file called `browserify.js` under /tasks/config. We will create a new task for Browserify.
```
module.exports = function(grunt) {

  grunt.config.set('browserify', {
    js: {
      src: 'browserify.js',
      dest: '.tmp/public/js/browserify-include.js'
    }
  });

  grunt.loadNpmTasks('grunt-browserify');
};
```
`src`: This is the file that browserify will read to see which module(s) need to be browserified<br/>
`dest`: This is where the browserified file will be stored
* Modify `/tasks/config/syncAssets.js`
```
module.exports = function (grunt) {
	grunt.registerTask('syncAssets', [
		'sync:dev',
		'browserify'   // <<< this added
	]);
};
```
* Modify `/tasks/config/compileAssets.js`
```
module.exports = function (grunt) {
	grunt.registerTask('compileAssets', [
		'clean:dev',
		'copy:dev',
		'browserify'   // <<< this added
	]);
};
```
* Done!

### What next?
Next time you run your server, grunt will create `browserify-include.js` and place it at the `dest` you mentioned. Simply include this file in your html page
```
<html>
  <head>
    <link src="/js/browserify-include.js"></link>
  </head>

  <body>
    <script>
      // Example usage of `underscore` library
      var union = underscore.union(["a","b"], ["x","y"]);
      console.log(union);
    </script>
  </body>
</html>
```
