---
title: "How to publish React components as an NPM package"
theme: "assets/night-owl.css"
separator: <--s-->
verticalSeparator: <--v-->
revealOptions: 
  transition: 'slide'
  showNotes: false
---

## Creating NPM Packages

<img src='assets/npm-logo.svg' alt='NPM Logo' style='width: 150px'></img>
<img src='assets/react-logo.svg' alt='React Logo' style='width: 150px'></img>

How to turn your React Components into a library you can download and use via node package manager

Note: For today's tech talk, I decided to speak briefly about how I turned a number of React components we intended to use among multiple websites into npm packages that we can import and use in any React project.

<--s-->

<!-- .slide: data-background="./assets/saturn-background.png" -->
## Our Libraries

* [apm-titan](https://www.npmjs.com/package/apm-titan)
* [apm-mimas](https://www.npmjs.com/package/apm-mimas)
* apm-saturn (*to be released*)
* slideshow (*to be released?*)

Note: At the moment we have two libraries up and running in beta: `apm-titan` and `apm-mimas`. We intend to call our root site generator **Saturn**, so we decided to name any React component packages after the moons of Saturn. Titan is Saturn's largest moon, so naturally it's the name of our biggest package that contains most of the "building blocks" we will use to make our sites. **Mimas** is a much smaller library used to generate images from our Images API, so it's named after the much smaller moon **Mimas**.

<--s-->

## In a nutshell

1. Create a blank local npm module
2. Set up the build process
3. Place your React components in there

Note: There are likely many other processes that would work, and there may be some that work even better, but this is the one that worked for me.

<--v-->

## Create a new project

Install `create-react-app` globally if you haven't already and run the command:

`create-react-app [your-package-name]`

<--v-->

## Cleanup

Delete all the files inside `src/` and create a new `index.js` file with the following starter code:

```
import React from 'react'
import { render } from 'react-dom'

const App = () => (
  <>
    <h1>This is my app</h1>
  </>
)

render(<App />, document.getElementById('root'))
```

<--v-->

## Create a Library

Create a new folder in `src` called `lib`.

This will serve as the root folder of the module published to npm.

<--v-->

## Drop in your components

Now you can drop your components into `src/lib`.

<img src='assets/vscode.png' alt='Your components will live in src/lib' style='width: 225px'></img>

Notes: We structure our projects so that each component has a folder, and that folder also contains any tests, documentation and stories for that component, but you can set up these component files however you like.

<--v-->

## Export your components

In `lib`, create another `index.js` file. Write an import and export statement for each of your components like so:

```
import Button from './Button'
export { Button }
```

<--v-->

## Test your components

Out of the box, you can run any jest tests you've placed in the src directory with the command `npm run test`.

If you want to use some other library, you'll have to set that up in `package.json` yourself.

<--v-->

## Make sure they work

If you wish to see your components in action, you can import them into `src/index.js` (the one not in the lib folder) and run `npm start` to see what everything looks like. 

<img src='assets/code(1).png' alt='Importing my Button component to make sure it works' style='width: 350px;margin: 10px'></img>
<img src='assets/localhost(1).png' alt='My Button component in action on localhost:3000' style='width: 350px;margin: 10px'></img>

Note: If you intend to set up Storybook for your project, you can also import check out your components there.

<--s-->

<!-- .slide: data-background="./assets/babel-background.png" -->
## Set up build process

To output a `/dist` folder for publication, we'll be replacing `create-react-app`'s "build" script with a [Babel](https://babeljs.io/) script of our own.

<--v-->

Run the command `npm i -D @babel/cli @babel/preset-env @babel/preset-react babel-preset-minify` to install it and some default settings to your dev dependencies. Then create a file `.babelrc` in the root of the project with the following contents:

```
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-react",
    "minify",
    [
      "react-app",
        { "absoluteRuntime": false }
    ]
  ]
}
```

<--v-->

## Replacing the build script

Inside the project's `package.json`, replace the `build` script with the following:

```
"build" : "rm -rf dist && NODE_ENV=production babel src/lib --out-dir dist --copy-files --ignore __tests__,spec.js,test.js,__snapshots__,'src/**/*.story.js','src/**/*.test.js'"
```

The command `npm run build` will now transpile the code inside `src/lib` into the folder `dist`.

Note: This `ignore` flag is unfortunately not working at the moment, so your test files and others will be copied up to the `/dist` directory anyway. I hope they'll fix it soon, so I included it here anyway, since this is really a better solution than just ignoring it or performing some sort of cleanup script immediately after.

<--v-->

Add the two following attributes to your `package.json`:

<--s-->

## Prepare for publishing

Remove the line `"private": true` from `package.json`, move `react-scripts`, `react` and `react-dom` from dependencies to devDependencies, and add the following lines:

```
"main": "dist/index.js",
"module": "dist/index.js",
"files": [ "dist", "README.md" ],
"repository": {
  "type": "git",
  "url": "URL_OF_YOUR_REPOSITORY"
},
"license": "MIT"
```

Note: You should also set the package's license to MIT and set `private` to `false`, like the other packages we have public on GitHub.

<--v-->

## Readme

Replace the text in the default `README.md` file with some useful information about what your package does and how to use it.

<--s-->

## Publication

Go to [npmjs.com](https://www.npmjs.com/) and create an account if you don't already have one. Then enter `npm login` in your terminal and answer the prompts for your **username**, **password** and **email**.

Note: You'll only have to do this part if you've never published to NPM before.

<--v-->

<!-- .slide: data-background="./assets/fireworks-background.png" -->
## Publish the dang thing

<img src='https://media.giphy.com/media/zBhZiVNNQjfTG/giphy.gif' alt='Yaaaasss gif' style='width: 350px;margin: 10px'></img>

Run `npm run publish`. That's it! Try downloading your package with `npm i [your-package-name]` and use it however you please.)

<--v-->

## Other useful commands

* [`npm-access`](https://docs.npmjs.com/cli/access.html): Set the access level on published packages
* [`npm-deprecate`](https://docs.npmjs.com/cli/deprecate): Deprecate version(s) of an application
* [`npm-unpublish`](https://docs.npmjs.com/cli/unpublish): Unpublish version(s) of an application

Note: You should generally avoid unpublishing things, especially if someone may already be relying on that version of a package. Also, you won't be able to re-publish to that particular version number for 24 hours after you run the command anyway. It's often safer and easier to just publish your newer changes and increment the patch number, then mark the previous version deprecated.

<--s-->

## Credits

* This tutorial was largely based on [this one](https://hackernoon.com/creating-a-library-of-react-components-using-create-react-app-without-ejecting-d182df690c6b) by Aakash N S for [HackerNoon](https://hackernoon.com/), but updated and edited to better fit our needs
* I also used a bit of [this tutorial](https://medium.freecodecamp.org/how-to-make-a-beautiful-tiny-npm-package-and-publish-it-2881d4307f78) by Jonathan Wood for [freeCodeCamp](https://medium.freecodecamp.org/) to remind myself how I logged in to NPM
* This presentation was made with [reveal-md](https://github.com/webpro/reveal-md), a library that uses [reveal.js](https://github.com/hakimel/reveal.js/) to create slides and transitions from a markdown file

<--v-->

### If you wish to review this presentation in the future, you can find it on my website

[kimthompson.me/react-component-npm-package](https://kimthompson.me/react-component-npm-package)