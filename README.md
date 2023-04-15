# Project: firebase-tasks
Little To do web applicaton to learn about Vue.js and Firebase.

## Tech stack
- Frontend: Vue 3
- Backend: Firebase
- CSS framework: Tailwind ~~Bumla?~~ ~~Bootstrap?~~

## Start from scratch
Download and install Node. Check version with 
```
node --version
```
Install Vue CLI.
```
npm install -g @vue/cli
```

Create a Vue project.
```
vue create tasks-project
```

Install Tailwind in project and initialize it. This will create a tailwind.config.js file.
```
npm install -D tailwindcss postcss autoprefixer
npx tailwind init -p
```

Add the following to the newly created tailwind.config.js file.
 ```
 content: [
    './public/index.html',
    './src/**/*.{vue,js,html}'
  ],
 ```

Build the CSS.
```
npx tailwindcss -i ./src/assets/tailwind.css -o ./dist/output.css --watch
```
The `--watch` argument is to rebuild the file upon changes.

Import the built css file in main.js
```
import './assets/tailwind.css'
```
## Start from existing project
Command *npm install* will install the project's dependencies which are listed in the *package.json* file. 
```
npm install
```

## IDE environment
`VSCode` with the following plugins:
- `Volar` which is made by the Vue creators and recommended by VSCode. It contains `Vetur` for syntax highlighting.
- `Tailwind CSS IntelliSense` which is made by the Tailwind creators.
- `Material Icon Theme` for the File | Preferences | Theme | FileIconTheme.

## Run the application
With command *npm run*, you can run scripts which are defined in the *package.json* file.

Serve the application locally with:
```
npm run serve 
```