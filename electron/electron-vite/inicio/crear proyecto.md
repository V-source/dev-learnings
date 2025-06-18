```bash

npm create @quick-start/electron@latest
```
salida esperada:

```bash

✔ Project name: … <electron-app>
✔ Select a framework: › vue
✔ Add TypeScript? … No / Yes
✔ Add Electron updater plugin? … No / Yes
✔ Enable Electron download mirror proxy? … No / Yes

Scaffolding project in ./<electron-app>...
Done.
```

You can also directly specify the project name and the template you want to use via additional command line options. For example, to scaffold an Electron + Vue project, run:

```bash

# npm 7+, extra double-dash is needed:
npm create @quick-start/electron@latest my-app -- --template vue
```


Currently supported template presets include:
| javascript   | typescript    |
|--------------- | --------------- |
| vanilla   | vanilla-ts   |
| vue | vue-ts  |
| svelte | svelte-ts   |
| react  | react-ts   |
| solid | solid-ts   |


See [create-electron](https://github.com/alex8088/quick-start/tree/master/packages/create-electron) for more details.
