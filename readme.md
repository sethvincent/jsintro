# Introduction to javascript workshop
> learn the basics of javascript.

## Getting started:
Open chrome.

Make sure you have a GitHub account, and log in.
[Create an account if you haven't yet.](http://github.com/signup/free)

### Open the javascript console
*Chrome:*

Windows: CTRL-SHIFT-J

Mac: COMMAND-OPTION-J

*Firefox:*

Windows: CONTROL-SHIFT-K

Mac: COMMAND-OPTION-K

We can type javascript into the console. This is also where messages are displayed when we use `console.log()`.

Once you're logged in to GitHub, open [requirebin.com](http://requirebin.com) in a new tab.


## javascript basics
- variables
- strings
- numbers
- arrays
- objects
- functions



```
// Here we select the canvas with an id of game 
// this is where we will draw our game assets
var canvas = document.querySelector('#game');

// What kind of drawing will we do?
// our game is in 2d, so our drawing will happen in the _context_ of 2 dimensions
var context = canvas.getContext('2d');

function startGame(){ 
  // set the width and height of our drawing canvas
  canvas.height = 400;
  canvas.width = 800;

  // start the game loop
  loop();
}

// any thing inside the loop() function gets run on every loop
function loop(){
  // this function runs the loop at a consistent rate, 
  // and only runs the loop when the browser tab is in focus
  requestAnimationFrame( loop, canvas );

  // update the game
  update();

  // draw new stuff after they've been updated
  draw();
}

// make a box object
// give it a starting x, y location, a width, height, speed, and color
var box = {
  x: 50,
  y: 50,
  width: 10,
  height: 10,
  speed: 10,
  color: '#4f5654'
};

// this function manages all the user input for the box object
box.input = function(){
  
  // check if any of the arrow keys are in the keysDown object
  if (40 in keysDown) {
    box.y += box.speed;
  }
  if (38 in keysDown) {
    box.y -= box.speed;
  }
  if (37 in keysDown) {
    box.x -= box.speed;
  }
  if (39 in keysDown) {
    box.x += box.speed;
  }
  
  // check if box hits left edge
  if (box.x <= 0) {
    box.x = 0;
  }

  // check if box hits right edge
  if (box.x >= canvas.width - box.width) {
    box.x = canvas.width - box.width;
  }

  // check if box hits top edge
  if (box.y <= 0) {
    box.y = 0;
  }

  // check if box hits bottom edge
  if (box.y >= canvas.height - box.height) {
    box.y = canvas.height - box.height;
  }

}

// draw the box
box.draw = function() {
  // set the color of the box
  context.fillStyle = box.color;

  // actually draw the box to the canvas
  context.fillRect(box.x, box.y, box.width, box.height);
};

// update the game
function update(){
  // check for any input relevant to the box
  // every time the game is updated
  box.input();
}

// draw on the canvas
function draw(){
  // this clears the canvas so that when the box is drawn each time
  // it looks like it moves, rather than drawing a line that follows the path
  // of the box. comment out the context.clearRect line to see what i mean.
  context.clearRect(0, 0, canvas.width, canvas.height);
  box.draw();
}

// this object contains any keyboard keys that are currently pressed down
var keysDown = {};

// here we add an event listener that watches for when the user presses any keys
window.addEventListener("keydown", function(e) {
  // e stands for event

  // add the key being pressed to the keysDown object
  keysDown[e.keyCode] = true;

  // if the user is pressing any of the arrow keys, disable the default
  // behavior so the page doesn't move up and down
  if (e.keyCode === 40 || e.keyCode === 38 || e.keyCode === 37 || e.keyCode === 39) {
    e.preventDefault();
  }
}, false);

// this event listener watches for when keys are released
window.addEventListener("keyup", function(e) {
  // and removes the key from the keysdown object
  delete keysDown[e.keyCode];
}, false);

// start the game
startGame();
```


## creating the same thing with crtrdg.js:

```
var inherits = require('inherits');
var Game = require('crtrdg-gameloop');
var Entity = require('crtrdg-entity');
var Keyboard = require('crtrdg-keyboard');


var canvas = document.createElement('canvas');
canvas.id = 'game';

var body = document.getElementsByTagName('body')[0];
body.appendChild(canvas);

inherits(Player, Entity);

function Player(options){
  this.position = { 
    x: options.position.x, 
    y: options.position.y 
  };

  this.size = {
    x: options.size.x,
    y: options.size.y
  };

  this.velocity = {
    x: options.velocity.x,
    y: options.velocity.y
  };
  
  this.speed = options.speed;
  this.friction = options.friction;
  this.color = options.color;
}

Player.prototype.move = function(velocity){
  this.position.x += velocity.x;
  this.position.y += velocity.y;
};

Player.prototype.checkBoundaries = function(){
  if (this.position.x <= 0){
    this.position.x = 0;
  }

  if (this.position.x >= this.game.width - this.size.x){
    this.position.x = this.game.width - this.size.x;
  }

  if (this.position.y <= 0){
    this.position.y = 0;
  }

  if (this.position.y >= this.game.height - this.size.y){
    this.position.y = this.game.height - this.size.y;
  }
};

Player.prototype.keyboardInput = function(){
  if ('A' in keyboard.keysDown){
    this.velocity.x = -this.speed;
  }

  if ('D' in keyboard.keysDown){
    this.velocity.x = this.speed;
  }

  if ('W' in keyboard.keysDown){
    this.velocity.y = -this.speed;
  }

  if ('S' in keyboard.keysDown){
    this.velocity.y = this.speed;
  }
};

var game = new Game({
  canvasId: 'game',
  width: window.innerWidth,
  height: window.innerHeight,
  backgroundColor: '#E187B8'
});

var keyboard = new Keyboard(game);

var player = new Player({
  position: { x: 10, y: 10 },
  size: { x: 10, y: 10 },
  velocity: { x: 0, y: 0 },
  speed: 3,
  friction: 0.9,
  color: '#fff'
});

player.addTo(game);

player.on('update', function(interval){
  this.keyboardInput(keyboard);

  this.move(this.velocity);
  this.velocity.x *= this.friction;
  this.velocity.y *= this.friction;

  this.checkBoundaries();
});

player.on('draw', function(draw){
  draw.fillStyle = this.color;
  draw.fillRect(this.position.x, this.position.y, this.size.x, this.size.y);
});
```

# Git/GitHub

Basics of using git:

Create a git repository:

```
cd name-of-folder
git init
```

Add files:

```
git add name-of-file

// or add all files in directory:

git add .
```

When you add files to a git repository they are "staged" and ready to be committed.

Remove files:
```
git rm name-of-file

// force removal of files:

git rm -rf name-of-file-or-directory
```

Commit files and add a message using the `-m` option:

```
git commit -m 'a message describing the commit'
```

Create a branch:

```
git branch name-of-branch
```

Checkout a branch:

```
git checkout name-of-branch
```

Shortcut for creating a new branch and checking it out:

```
git checkout -b name-of-branch
```

Merge a branch into the master branch:

```
git checkout master
git merge name-of-branch
```

Add a remote repository:

```
git remote add origin git@github.com:yourname/projectname.git
```

List associated repositories:

```
git remote -v
```

Pull changes from a remote repository:

```
git pull origin master
```

Push changes to a remote repository

```
git push origin master
```

Checkout a remote branch:

```
git checkout -t origin/haml
```

## Getting started with `npm`
If you've alreadt installed node.js, you've got npm.

Check the version of `npm` like this:

```
npm -v
```

This should return something like:

```
1.2.32
```

The exact version numbers might differ, and that's ok.

Installing a module will automatically place the module in a folder named `node_modules` in your current working directory.

Make a new folder:

```
mkdir npm-experiments
cd npm-experiments
```

Install browserify:

```
npm install browserify
```

Now, if you look in the node_modules directory, you'll see browserify!

You can also install modules globally, typically so that you can run their commands at any time in any directory. This is useful with a module like browserify, so let's try it out:

```
npm install -g browserify
```

It's the `-g` option that install the module globally.

You can delete the node_modules directory:

```
rm -rf node_modules
```

And run the browserify command:

```
browserify
```

Without any options it'll only return help text. For more about browserify, check out the Introduction to browserify section.

To learn more about `npm` run the command without any options:

```
npm
```

This gives you a full list of the commands and options available through `npm`. To learn about any particular command you can run:

```
npm help name-of-command
```

## Creating a module
Any time you're using javascript modules from `npm` in a project you should create a package.json file. In this file you can save the dependencies for your project, along with license, author, repo inforamtion, and other details.

You can use the `npm init` command to generate a package.json file:

```
npm init
```

Answer the questions.

When you're done, you'll have a package.json file. You can install modules and save them as dependencies of your project like this:

```
npm install request --save
```

And if you are installing a module (like a test framework) as a development dependency, you can do so like this:

```
npm install tap --save-dev
```

## Publishing modules
Once you've written a module, you can publish it to `npm` super easy:

```
npm publish .
```

Before running the `npm publish` command you'll want to edit your package.json file to make sure that properties like version, author, homepage, and repository are all filled in. You should also first create a useful readme.md file, as that is displayed on a modules project page on [npmjs.org](http://npmjs.org).

## Finding modules
You can check out [npmjs.org](http://npmjs.org) as well as [npmsearch.com](http://npmsearch.com) to find modules that you can use in your projects.

You can also run the `search` command in the terminal:

```
npm search template
```

This will return a bunch of modules related to templates!

## Bower

```
npm install -g bower
```

```
bower install jquery backbone
```