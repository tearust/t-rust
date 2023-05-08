# Sample Front-end

Although there are a lot of UI changes here, the under the hood changes are minimal. And most of them are just pure web front end code so we don't need to explain those. So let's focus on the logic related code in `views/TaskMain.vue`.

## Query tasks

````
    async refreshList(){
      this.$root.loading(true);
      const list = await layer2.task.queryTaskList(this);
      this.list = list;
      this.$root.loading(false);
    },
````

Follow the queryTaskList to the task.js

````
  async queryTaskList(self){
    const rs = await txn.query_request('query_task_list', {
      address: self.layer1_account.address,
    });
    return _.map(rs.list, (item)=>{
      try{
        item.price = utils.layer1.balanceToAmount(item.price);
        item.deposit = utils.layer1.balanceToAmount(item.required_deposit);
      }catch(e){
        item.price = utils.layer1.balanceToAmount(utils.toBN('0x'+item.price).toString());
        item.deposit = utils.layer1.balanceToAmount(utils.toBN('0x'+item.required_deposit).toString());
      }
      return item;
    });
  }
````

The function txn.query_request is the utility that does most of the heavy lifting. We've explained this utility and others like it before. 

## Create New Task

In TaskMain.vue:

````
    async createNewTask(){
      try{
        await layer2.task.createNewTask(this, {}, async (rs)=>{
          this.$root.success("create task success");
          await this.refreshList();
        });
      }catch(e){
        this.$root.showError(e);
      }
    },
````

Similiarly, we dive into task.js:

````
async createNewTask(self, param={}, succ_cb){
    const session_key = user.checkLogin(self);

    const tappId = base.getTappId();
    self.$store.commit('modal/open', {
      key: 'common_form',
      param: {
        title: 'Create Task',
        text: ``,
        props: {
          subject: {
            type: 'Input',
            required: true,
            label: 'Task subject',
          },
          price: {
            type: 'number',
            default: 10,
            label: 'Price (TEA)'
          },
          required_deposit: {
            type: 'number',
            default: 20,
            label: 'Deposit (TEA)',
          }

        },
      },
      cb: async (form, close) => {
        self.$root.loading(true);
        const price = utils.layer1.amountToBalance(form.price);
        const required_deposit = utils.layer1.amountToBalance(form.required_deposit);

        const param = {
          address: self.layer1_account.address,
          tappIdB64: tappId,
          authB64: session_key,
          price: utils.toBN(price).toString(),
          requiredDeposit: utils.toBN(required_deposit).toString(),
          subject: form.subject,
        };

        try {
          await txn.txn_request('create_task', param);
          self.$root.success();
          await succ_cb();
        } catch (e) {
          console.error(e);
          self.$root.showError('Create task failed.');
        }
        close();
        self.$root.loading(false);

      }
    });
  },
````

First we get the session_key. This is used as Auth_b64 that we explained before, and will be referenced as `authB64: session_key` later.

A modal window will then open up showing a form where the user can input the content. Once commited, it will call the `cb` callback function. This callback function gathers all input task parameters, then sends them back via `txn.txn_request`. After that, we close the modal and clear the Loading backdrop. 

## Other operation: delete, task, complete

These operations follow the same pattern but are even simpler.

## Summary

So you may have noticed the pattern we used in the front end.

It will be either a query_request or a txn_request. This is exactly the same as we 've done for more than a decade in traditional web2 development. We've worked hard to make the TEA Project a smooth onramp from web2 to web3.
