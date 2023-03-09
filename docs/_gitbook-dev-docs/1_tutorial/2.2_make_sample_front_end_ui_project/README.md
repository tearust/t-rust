In this step, you will learn

* clone the sample-front-end as the boilderplate for the front end
* setup environment
* run the front end
* send say-hello request and check response

https://github.com/tearust/sample-front-end is the front end boilerplace we will use in our tutorial. It has no real function but a button that clicked to send a "say-hello" json response to the sample-actor. When response received, it shows an alert on the UI. 

## Clone the front end boilerplate

`git clone git@github.com:tearust/sample-front-end.git` to your local folder.

## Setup environment

Make sure you have node.js v14 installed. If not, go to https://nodejs.org/en/ to install

Enter the project folder `cd sample-front-end`, then run `npm i` to install dependencies.

If your backend has different IP or port number rather than default localhost:8000, please edit the `.env.test` file to your customized value:

````
VUE_APP_LAYER2_URL=http://127.0.0.1:8000
````

This address will be your backend service address. 

## Run the front end

Run `npm start`

Open a browser and vlisit \[[http://127.0.0.1:3200](http://127.0.0.1:3200/)\].

You should see an page with a text "Welcome to Sample Actor testing page." and a single button "click here to send request".

Please make sure your dev-runner is up running. If not, follow the instruction on the previous steps. If the backend runs correctly, you can click the button to send a request to the sample-actor. You should see the response in a browser alert: 

````
{"data":"Hello world!","status":200,"statusText":"OK","headers":{"content-length":"14","content-type":"application/json"},"config":{"url":"/say-hello","method":"post","data":"{\"actor\":\"someone.sample\",\"address\":\"0x000000000000000000000000000000000000000f\"}","headers":{"Accept":"application/json, text/plain, */*","Content-Type":"application/json;charset=utf-8"},"baseURL":"http://127.0.0.1:8000","transformRequest":[null],"transformResponse":[null],"timeout":0,"xsrfCookieName":"XSRF-TOKEN","xsrfHeaderName":"X-XSRF-TOKEN","maxContentLength":-1,"maxBodyLength":-1},"request":{}}
````

Congratulations! Your first TApp is correctly running in your local development environment. 

In the next step, you will learn how to deploy it to the testnet.
