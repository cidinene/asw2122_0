# VSCODE AND JEST DEBUB

We are going to configure our VS Code workspace to debug our test.  

## LAUNCH OUR APP

1 - First we launch the restapi app. 

```
cd restapi
npm start
```

2 - In another terminal,  we launch the webapp app

```
cd webapp
npm start
```

4 - We check *jest , jest-puppeteer, jest-cucumber and puppeteer* are installed 
```
npm list
```

3 - We create a *launch.json* to debug our jest app with following configuration
```
{
    "version": "0.2.0",
    "configurations": [
      {
        "name": "Debug E2E Test",
        "type": "node",
        "request": "launch",
        "runtimeExecutable": "${workspaceRoot}/webapp/node_modules/.bin/jest",
        "args": ["--config=jest.config.ts", "--runInBand", "--no-cache", "--env=jsdom", "--watchAll"],
        "cwd": "${workspaceRoot}/webapp/e2e",
        "protocol": "inspector",
        "console": "integratedTerminal",
        "internalConsoleOptions": "neverOpen"
      }
    ]
}
```

4 - We increase Timeouts 

![timeout](https://user-images.githubusercontent.com/29120610/160781666-606caeaa-a41e-40bc-a67a-24cd899db387.png)

5 - As *beforeAll* function is async , is a good idea define depending  functions as async

![sync](https://user-images.githubusercontent.com/29120610/160782288-45da669e-20c9-4c9b-89b1-cead7d4d07f7.png)









