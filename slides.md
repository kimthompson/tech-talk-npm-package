---
title: "How to publish React components as an NPM package"
separator: <--s-->
verticalSeparator: <--v-->
---

# Creating NPM packages

How to turn your React Components into a library you can download and use via node package manager

Note: For today's tech talk, I decided to speak briefly about how I turned a number of React components we intended to use among multiple websites into npm packages that we can import and use in any React project. (TODO: picture of NPM and/or React logos?)

<--s-->

## Our Libraries

* [apm-titan](https://www.npmjs.com/package/apm-titan)
* [apm-mimas](https://www.npmjs.com/package/apm-mimas)
* apm-saturn (*to be released*)
* slideshow (*to be released?*)

Note: At the moment we have two libraries up and running in beta: `apm-titan` and `apm-mimas`. We intend to call our root site generator **Saturn**, so we decided to name any React component packages after the moons of Saturn. Titan is Saturn's largest moon, so naturally it's the name of our biggest package that contains most of the "building blocks" we will use to make our sites. **Mimas** is a much smaller library used to generate images from our Images API, so it's named after the much smaller moon **Mimas**. (TODO: have a picture of Saturn and its moons in the background)

<--s-->

## My process

Create a new package, set up the build process, and start plopping your reusable components in there.

Note: There are likely many other processes that would work, and there may be some that work even better, but this is the one that worked for me and that made the most sense to me by the time I did it twice.

<--v-->

## Create a new project

Install `create-react-app` globally if you haven't already and run the command

`create-react-app [your-package-name]`

<--v-->

## Cleanup

Delete all the files inside `src/` and create a new `index.js` file with some starter code:

```
import React from 'react';
import { render } from 'react-dom';

const App = () => (
  <>
    <h1>This is my app</h1>
  </>
);

render(<App />, document.getElementById('root'));
```

<--v-->

## Create a Library

Create a new folder in `src` called `lib`. This will serve as the root folder of the module published to npm.

<--v-->

## Drop in your components

Now you can drop your components into `src/lib`. We structure our projects so that each component has a folder, and that folder also contains any tests, documentation and stories for that component, but you can set up these component files however you like.

Note: (TODO: image of what the directory looks like at this point plus the upcoming index.js file)

<--v-->

## Exporting your components

In `lib`, create another `index.js` file. Write an import and export statement for each of your components like so:

```
import Button from "./Button";
export { Button };
```

<--v-->

## Testing your components

Out of the box, you can run any jest tests you've placed in the src directory with the command `npm run test`. If you want to use some other library, you'll have to set that up in `package.json` yourself.

<--v-->

## Making sure they work

If you wish to see your components in action, you can import them into `src/index.js` (the one not in the lib folder) and run `npm run start` to see what everything looks like. 

Note: If you intend to set up Storybook for your project, you can also import check out your components there. (TODO: image of Button component(s) being rendered on localhost:3000). 

<--s-->

## Setting up the build process

To output a `/dist` folder for publication, we'll be replacing `create-react-app`'s "build" script with a [Babel](https://babeljs.io/) script of our own.

<--v-->

Run the command `npm i -D @babel/cli @babel/preset-env @babel/preset-react` to install it and some default settings to your dev dependencies. Then create a file `.babelrc` in the root of the project with the following contents:

```
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-react",
    [
      "react-app",
        { "absoluteRuntime": false }
    ]
  ]
}
```

Note: (TODO: Babel logo in background?)

<--v-->

## Replacing the build script

Inside the project's `package.json`, replace the `build` script with the following:

```
"build" : "rm -rf dist && NODE_ENV=production babel src/lib --out-dir dist --copy-files --ignore __tests__,spec.js,test.js,__snapshots__,'src/**/*.story.js','src/**/*.test.js'"
```

The command `npm run build` will now transpile the code inside `src/lib` into the folder `dist`.

Note: This `ignore` flag is unfortunately not working at the moment, so your test files and others will be copied up to the `/dist` directory anyway. I hope they'll fix it soon, so I included it here anyway, since this is really a better solution than just ignoring it or performing some sort of cleanup script immediately after. (TODO: Image of `/dist` directory produced by this?)

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

Note: You should also set the package's license to MIT, like the other packages we have public on GitHub.

<--v-->

## Readme

Replace the text in the default `README.md` file with some useful information about what your package does and how to use it.

<--s-->

## Publication

Go to [npmjs.com](https://www.npmjs.com/) and create an account if you don't already have one. Then enter `npm login` in your terminal and answer the prompts for your **username**, **password** and **email**.

Note: You'll only have to do this part if you've never published to NPM before.

<--v-->

## Publish the dang thing

`npm run publish`

That's it! Try downloading your package with `npm i [your-package-name]` and use it however you please.

Note: (TODO: Fireworks background or something silly like that (a gif or video?))

<--v-->

## Other useful commands

* [`npm-access`](https://docs.npmjs.com/cli/access.html): Set the access level on published packages
* [`npm-deprecate`](https://docs.npmjs.com/cli/deprecate): Deprecate version(s) of an application
* [`npm-unpublish`](https://docs.npmjs.com/cli/unpublish): Unpublish version(s) of an application

Note: You should generally avoid unpublishing things, especially if someone may already be relying on that version of a package. Also, you won't be able to re-publish to that particular version number for 24 hours after you run the command anyway. It's often safer and easier to just publish your newer changes and increment the patch number, then mark the previous version deprecated.

<--s-->

## Credits

* This tutorial was largely based on [this one](https://hackernoon.com/creating-a-library-of-react-components-using-create-react-app-without-ejecting-d182df690c6b) by Aakash N S for [HackerNoon](https://hackernoon.com/), but updated and edited to better fit our needs.
* I also used a bit of [this tutorial](https://medium.freecodecamp.org/how-to-make-a-beautiful-tiny-npm-package-and-publish-it-2881d4307f78) by Jonathan Wood for [freeCodeCamp](https://medium.freecodecamp.org/) to remind myself how I logged in to NPM
* This presentation was made with [reveal-md](https://github.com/webpro/reveal-md), a library that uses [reveal.js](https://github.com/hakimel/reveal.js/) to create slides and transitions from a markdown file. I absolutely loved using it, especially compared to PowerPoint.

<--v-->

### If you wish to review this presentation in the future, you can find it on my website

[kimthompson.me/react-component-npm-package](https://kimthompson.me/react-component-npm-package)