In this step, you'll learn:

- [[#Clone the front end boilerplate|Clone the sample-front-end as the boilerplate for the front end.]]
- [[#Setup environment|Setup the environment.]]
- [[#Run the front end|Run the front end.]]
- Send a "say-hello" request and check the response.

[https://github.com/tearust/sample-front-end](https://github.com/tearust/sample-front-end) is the front end boilerplate we'll use in our tutorial. It has no real function other than a button that's clicked to send a "say-hello" json response to the sample-actor. When a response is received, it shows an alert in the UI. 

## Clone the front end boilerplate

Run `git clone git@github.com:tearust/sample-front-end.git` to your local folder.

## Setup your environment

Make sure you have node.js v14 installed. If not, go to [Nodejs.org](https://nodejs.org/en/) to install.

Enter the project folder `cd sample-front-end`, then run `npm i` to install dependencies.

If your backend has different IP or port number other than the default localhost:8000, please edit the `.env.test` file to edit in your customized values:

```
VUE_APP_LAYER2_URL=http://127.0.0.1:8000
```

This address will be your backend service address. 

## Run the front end

Run `npm start`

Open a browser and vlisit [[http://127.0.0.1:3200](http://127.0.0.1:3200/)].

You should see a page with the text "Welcome to Sample Actor testing page." and a single button "click here to send request".

Please make sure your dev-runner is up and running. If not, follow the instructions in the previous steps. If the backend is running correctly, you can click the button to send a request to the sample-actor. You should see the response in a browser alert: 

```
{"data":"Hello world!","status":200,"statusText":"OK","headers":{"content-length":"14","content-type":"application/json"},"config":{"url":"/say-hello","method":"post","data":"{\"actor\":\"someone.sample\",\"address\":\"0x000000000000000000000000000000000000000f\"}","headers":{"Accept":"application/json, text/plain, */*","Content-Type":"application/json;charset=utf-8"},"baseURL":"http://127.0.0.1:8000","transformRequest":[null],"transformResponse":[null],"timeout":0,"xsrfCookieName":"XSRF-TOKEN","xsrfHeaderName":"X-XSRF-TOKEN","maxContentLength":-1,"maxBodyLength":-1},"request":{}}
```

Congratulations! Your first TApp is correctly running in your local development environment. 

In the next step, you'll learn how to deploy it to the testnet.
