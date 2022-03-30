# VSCODE AND JEST DEBUB

We are going to configure our VS Code workspace to debug our test.  

## LAUNCH OUR APP WITH DEBUG

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
        "name": "Debug E2E Tests",
        "type": "node",
        "request": "launch",
        "runtimeExecutable": "${workspaceRoot}/webapp/node_modules/.bin/jest",
        "args": ["--config=./e2e/jest.config.ts", "--rootDir=e2e" ,"--runInBand", "--no-cache", "--env=jsdom", "--watchAll"],
        "cwd": "${workspaceRoot}/webapp",
        "protocol": "inspector",
        "console": "integratedTerminal",
        "internalConsoleOptions": "neverOpen""
      }
    ]
}
```
4 - We Launch our configuration with berakpoint.

## TIPS
1 - We increase Timeouts 

![timeout](https://user-images.githubusercontent.com/29120610/160781666-606caeaa-a41e-40bc-a67a-24cd899db387.png)

2 - As *beforeAll* function is async , is a good idea define depending  functions as async

![sync](https://user-images.githubusercontent.com/29120610/160782288-45da669e-20c9-4c9b-89b1-cead7d4d07f7.png)

3 - We can test to launch puppeteer with *headless: false* and *headless: true*

```
beforeAll(async () => {
    console.log("Before All- getting browser")
    browser = process.env.GITHUB_ACTIONS  
      ? await puppeteer.launch()
      : await puppeteer.launch({ headless: false });
    page = await browser.newPage();
```


## LAUNCH OUR APP FOR CI

For CI , our webapp project contains a npm script **test:e2e** that launch our rest api server and webapp server . After that , it launch test. 

```
 "test:e2e": "start-server-and-test 'npm --prefix ../restapi start' http://localhost:5000/api/users/list prod 3000 'jest --rootDir=e2e --config=./e2e/jest.config.ts'",
```









