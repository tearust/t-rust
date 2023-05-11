No doubt, there are some front end UI changes, but we are going to ignore most of them while only focus on the key changes related to the retweet verification logic.

After the worker click "complete" button, this is where the new business logic kicks in. It will trigger a secured oracle API call at the backend, verify if this is a successful retweet. 

Let's take a look at the front end code trigged by the "complete" button:

In src/layer2/task.js inside the `async completeTask` callback part
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

First, it takes the tweet url, then put into the opts. Then send a txn request: `await txn.txn_request('complete_task', opts);`
Right after the txn_request, it starts to `await succ_cb()`. This make the front end wait for the successful return from the backend, then end the waiting. 

This is pretty much what front end is supposed to do. Very simple, right? The main logic happens in the backend.


