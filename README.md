# her-care-calculator

## Deploy

Go to root folder of this repo.

```
chmod +x deploy.sh
./deploy.sh
```

which will build the production version of the app and deploy it

## Deployment Config

`vue.config.js` specifies the repo name
`deploy.sh` creates a dist folder, creates a git repo in the dist folder, and deploys it to the github pages branch. It should create the branch if it doesnt alright exist.

## Project setup

```
npm install
```

### Compiles and hot-reloads for development

```
npm run serve
```

### Compiles and minifies for production

```
npm run build
```

### Lints and fixes files

```
npm run lint
```

### Customize configuration

See [Configuration Reference](https://cli.vuejs.org/config/).
