# Sample Front-end Code Walkthrough
The `master` branch of the tutorial is the simple "hello world" example. There's not much front-end logic to explain. In contrast, the `login` branch is a  **typical** TApp boilerplate. We'll dig into more detail when we get there in our next step.

For the current `master` branch, you can see a typical VUE front-end Single Page Application. 

The only hello world related code is in `Home.vue`

This is the code:

```
export default {
  data(){
    return {};
  },
  methods: {
    get_env(key) {
      const x_key = 'VUE_APP_' + _.toUpper(key);
      return _.get(process.env, x_key, null);
    },
    async send_request(){
      const _axios = axios.create({
        baseURL: this.get_env('LAYER2_URL'),
      });
      const rs = await _axios.post('/say-hello', {
        actor: 'someone.sample',
        address: '0x000000000000000000000000000000000000000f'
      });
      alert(JSON.stringify(rs));
    }
  }
};
```

It simply send an axios post to `/say-hello` with the following JSON content:

```
{
    actor: 'someone.sample',
    address: '0x000000000000000000000000000000000000000f'
}
```

This is an example of how we test the sample-actor using CURL or Postman.

Another thing we need to mention is the LAYER2_URL env var. This is the address to the [backend actor](../../../z_glossary/back_end_actor.md). It's defined in the `.env.test` file.

```
NODE_ENV = dev
VUE_APP_LAYER2_URL=http://127.0.0.1:8000


```

If you have a special environment settings that the backend is not running on 127.0.0.1:8000, please make sure you change this value. 

