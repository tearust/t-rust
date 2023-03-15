The `master` branch of tutorial is the simple "hello world". There is not much front end logic to explain. However, the `login` branch will be a **typical** TApp boilderplate. We will deep into detail when we get there in our next step.

For the current `master` branch, you can see the typical VUE front end Single Page Application. 

The only hello world related code is in `Home.vue`

The code 
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

It simply send a axios post to `/say-hello` with the JSON content
```
{
    actor: 'someone.sample',
    address: '0x000000000000000000000000000000000000000f'
}
```

This is example how we test the sample-actor using CURL or Postman.

Another thing we need to mention is the LAYER2_URL env var. This is the address to the backend actor. It is defined in the `.env.test` file 
```
NODE_ENV = dev
VUE_APP_LAYER2_URL=http://127.0.0.1:8000


```

If you have a special environment settings that the backend is no running on 127.0.0.1:8000, please make sure you change this value. 

