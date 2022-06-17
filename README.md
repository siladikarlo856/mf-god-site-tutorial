# Vue on Module Federation | Tutorial by Jack Herrington

Official tutorial: https://www.youtube.com/watch?v=ICeH3uBGGeo 

The apps will be created using: https://www.npmjs.com/package/create-mf-app 

The final code can be found on GitHub: 

## Step 1: Create home app
```
npx create-mf-app
```
Settings
```
? Pick the name of your app: home
? Project Type: Application
? Port number: 8080
? Framework: vue3
? Language: javascript 
? CSS: CSS
```
Install packages and start the dev server
```
cd home
npm i 
npm start
```
Delete content from index.css and replace the content within App.vue with the following:
```
<style>
.container {
  width: 800px;
  margin: auto;
  padding: 1rem;
}
.carousel {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-column-gap: 1rem;
}
.carousel > img {
  width: 100%;
}
</style>

<template>
<div class="container">
  <h1>Great Dogs Deserve Great Homes</h1>
  <p>We are an adoption agency committed to putting wonderful adoptable dogs
  into great permanent homes...</p>

  <h1>Adoptable Dogs</h1>
  <div class="carousel">
    <img src="https://placedog.net/500/280?id=5" alt="Dog 5">
    <img src="https://placedog.net/500/280?id=6" alt="Dog 6">
    <img src="https://placedog.net/500/280?id=7" alt="Dog 7">
  </div>
</div>
</template>
```
## Step 2: Create dog-detail app
```
npx create-mf-app
```
Settings
```
? Pick the name of your app: dog-detail
? Project Type: Application
? Port number: 8081
? Framework: vue3
? Language: javascript 
? CSS: CSS
```
Install packages and start the dev server
```
cd dog-detail
npm i 
npm start
```
Delete content from index.css and replace the content within App.vue with the following:
```
<style>
.container {
  width: 800px;
  margin: auto;
  padding: 1rem;
}
.detail {
  display: grid;
  grid-template-columns: 30% 70%;
  grid-column-gap: 1rem;
}
.detail img {
  width: 100%;
}
.carousel {
  width: 800px;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-column-gap: 1rem;
}
.carousel img {
  width: 100%;
}
</style>

<template>
<div class="container">
  <div class="detail">
    <img src="https://placedog.net/500/280?id=40" />
    <div>
      <h1>Cindy</h1>
      <p>Cindy is a fabulous dog with a great personality...</p>
    </div>
  </div>
  <hr />
  <h1>More Adoptable Dogs</h1>
  <div class="carousel">
    <div>
      <img src="https://placedog.net/500/280?id=60" alt="Dog 60">
      <h1>Cutie</h1>
    </div>
    <div>
      <img src="https://placedog.net/500/280?id=61" alt="Dog 61">
      <h1>Ace</h1>
    </div>
    <div>
      <img src="https://placedog.net/500/280?id=62" alt="Dog 62">
      <h1>Spot</h1>
    </div>
  </div>
</div>
</template>
```
## Step 3: Expose Header component from ‘home’ app and consume it in ‘dog-detail’ app

Create Header component src/Header.vue
```
<style>
header {
  background: lightblue;
  padding: 1rem;
  font-size: xx-large;
  font-weight: bold;
  text-align: center;
}
</style>

<template>
<header>
  !!!Spot's A Good Dog!!!
</header>
</template>

The component can be used in the project

<template>
<div class="container">
  <Header />
...
</template>

<script>
import Header from './Header.vue'

export default {
  components: {
    Header,
  }
}
</script>
```


Expose the Header component  from the home app by editing home app > webpack.config.js
```
exposes: {
  './Header': './src/Header.vue'
},
```

Consume the Header component for the home app in the dog-detail app.

First, edit dog-detail > webpack.config.js
```
remotes: {
  home: 'home@http://localhost:8080/remoteEntry.js'
},
```
Use the component in the app
```
<template>
<div class="container">
  <Header />
  ...
</template>

<script>
  import Header from 'home/Header';

  export default {
    components: {
      Header,
    }
  }
</script>
```

Step 4: Expose Carousel component from ‘dog-detail’ app and consume it in ‘home’ app

Create a component in src/Carousel.vue

```
<style>
.carousel {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-column-gap: 1rem;
}
.carousel img {
  width: 100%;
}

.carousel h1 {
  text-align: center;
  margin-top: 0;
}
</style>

<template>
  <div class="carousel">
    <div>
      <img src="https://placedog.net/500/280?id=60" alt="Dog 60" />
      <h1>Cutie</h1>
    </div>
    <div>
      <img src="https://placedog.net/500/280?id=61" alt="Dog 61" />
      <h1>Ace</h1>
    </div>
    <div>
      <img src="https://placedog.net/500/280?id=62" alt="Dog 62" />
      <h1>Spot</h1>
    </div>
  </div>
</template>
```
Use the component in dog-detail > App.vue
```
<template>
...
  <Carousel />
</div>
</template>

<script>
  import Header from 'home/Header';
  import Carousel from './Carousel.vue';

  export default {
    components: {
      Header,
      Carousel,
    }
  }
</script>
```
Expose component using MF plugin in dog-details > webpack.config.js file
```
new ModuleFederationPlugin({
  name: "dog_detail",
  name: "dogDetail",
  filename: "remoteEntry.js",
  remotes: {
    home: 'home@http://localhost:8080/remoteEntry.js'
  },
  exposes: {},
  exposes: {
    './Carousel': './src/Carousel.vue',
  },
  shared: require("./package.json").dependencies,
}),
```
Consume component using MF plugin in home app by editing home > webpack.config.js
```
remotes: {
  dogDetail: 'dogDetail@http://localhost:8081/remoteEntry.js'
},
```
And use it in home > App.vue
```
<template>
  ....  
  <Carousel />
</div>
</template>

<script>
import Header from './Header.vue';
import Carousel from 'dogDetail/Carousel';

export default {
  components: {
    Header,
    Carousel,
  }
}
```

## Step 5: Consume Vue Header component in React app ‘react-hots’ 
```
npx create-mf-app
```
Settings
```
? Pick the name of your app: react-host
? Project Type: Application
? Port number: 8082
? Framework: react
? Language: javascript
? CSS: CSS
```
Install packages and start the dev server
```
cd dog-detail
npm i 
npm start
```
Consume remote MF component by adding MF plugin in webpack.config.js
```
 plugins: [
    new ModuleFederationPlugin({
      name: "react_host",
      filename: "remoteEntry.js",
      remotes: {
        home: 'home@http://localhost:8080/remoteEntry.js',
      },
      ....
```
In the App.jsx fill add
```
import mountHeader from "home/mountHeader";
...
mountHeader('#header');

const App = () => (
....
```
## Step 6: Dynamic Vue component loading 

Add a <button> element with onClick handler 
```
<div>
  <button onClick={launchHeader}>Launch Header</button>
</div>
```
On button click, the module shall be loaded and initiated. The mountHeader is a module, not a function so the default shall be used.
```
const launchHeader = () => {
  import('home/mountHeader')
    .then(mountHeader => {
      mountHeader.default('#header');
    }) 
}
```
