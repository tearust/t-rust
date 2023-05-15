# Retweet Frontend
There are superficial UI differences when comparing the Retweet Task app to the more general Task app. But we're going to ignore most of them while only focusing on the key changes related to the retweet verification logic.

After the task taker clicks the "complete" button, this is where the new business logic kicks in. It will trigger a secure oracle API call in the backend and verify that this is a successful retweet. 

Let's take a look at the front end code trigged by the "complete" button, which is located at `src/layer2/task.js` and specifically inside the `async completeTask` callback section:

```
cb: async (form, close) => {
        self.$root.loading(true);

        let tweet = form.text;
        if(reg_tweet.test(tweet)){
          const arr = tweet.match(reg_tweet);
          tweet = arr[2];
        }

        const opts = {
          address: self.layer1_account.address,
          tappIdB64: base.getTappId(),
          authB64: session_key,
          subject: param.subject,
          text: tweet
        };

        try {
          await txn.txn_request('complete_task', opts);
          await succ_cb();
        } catch (e) {
          console.error(e);
          self.$root.showError(e.toString());
          if(error_cb){
            await error_cb();
          }
        }
        close();
        self.$root.loading(false);

      }
```


This code first takes the tweet url then parses and puts it into the opts. After that it sends a txn request: `await txn.txn_request('complete_task', opts);`.

Right after the txn_request, it starts to `await succ_cb()`. This makes the front end wait for the successful return from the backend, at which point the waiting ends. 

This front end logic is fairly basic and is typical of what a front end is supposed to do. The majority of the main logic happens in the backend.


