Although there are a lot of UI changes, but the changes are minimal. And most of them are just pure web front end code, we do not need to explain those. So let's focus on the logic related code in `views/TaskMain.vue`.

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
      item.price = utils.layer1.balanceToAmount(item.price.replace('0x', ''));
      item.deposit = utils.layer1.balanceToAmount(item.required_deposit.replace('0x', ''));
      return item;
    });
  }
````

The function txn.query_request is the utility that did the most heavy lifting. We have explained this utility and others before. 

## Create New Task

In TaskMain.vue

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

Similiarly, we dive into task.js

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

First we get the session_key. This is used as Auth_b64 that we explained before. See `authB64: session_key` later.

Then model open to show a form, user can input the content, once commit, it will call the `cb` callback function. This callback function gather all input task parameters, then `txn.txn_request`. After that, close the model then clear the Loading backdrop. 

## Other operation: delete, task, complate

Those operations follow the same pattern but simpler.

## Summary

So you may noticed the pattern we used in the front end.

It will be either a query_request or a txn_request. This is exactly the same as we have done for more than a decade on the traditional web2 development. So the TEA Project is a smooth ramp from web2 to web3.
