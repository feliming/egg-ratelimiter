# egg-ratelimiterplus

Rate limiter for Egg.js backed by Redis.
Fork 自：https://github.com/ZQun/egg-ratelimiter

- 支持 egg [js] [ts]
- 支持 Controller 控制器内分别配置请求速率限制
- 支持设置限流时候的响应头 Content-Type，默认是 `text/plain; charset=utf-8`，可以设置为 `application/json`

## Install

```bash
$ npm i egg-ratelimiterplus --save
```

你还需要安装[egg-redis](https://www.npmjs.com/package/egg-redis) 具体参考文档

```shell
npm i egg-redis --save
```

## Configuration

Change `${app_root}/config/plugin.ts` to enable ratelimiter plugin:

```typescript
export = {
  ratelimiterplus: {
    enable: true,
    package: 'egg-ratelimiterplus',
  }
}
```

Configure ratelimiter information in `${app_root}/config/config.default.ts`:

**Instructions**

```typescript
config.ratelimiterplus = {
    db: {},//如已配置egg-redis 可删除此配置
    router: [
      {
        path: '/',//限制路由路径 此规则不会匹配(index.html?id=1)[http://url/index.html?id=1]
        max: 5,
        time: '7s', //时间单位 s m h d y ...
        message: 'Custom request overrun error message'//自定义请求超限错误信息
      },
      {
        path: '/api',
        max: 5,
        time: '7s', //时间单位 s m h d y ...
        message: 'Custom request overrun error message'//自定义请求超限错误信息
      }
    ]
}

//如果不在db中初始化redis，则需要启用egg-redis
config.redis = {
    client: {
      port: 6379,          // Redis port
      host: '127.0.0.1',   // Redis host
      password: null,
      db: 0,
    },
}
```

Configure ratelimiter information in `${app_root}/app/controller/home.ts`:

**Controller**

```typescript
export class HomeController {

  @get('/index')
  public async index(ctx?): Promise<any> {
    const { ctx, service } = this;

    // 超出速率限制返回判断
    // [true]为超出速率限制   [false]则继续向下执行
    // js返回方法为 ctx.body = 'message'
    if(await ctx.Limit({ max: 5, time: '5s' })) return 'Rate limit exceeded'


    //...
    //业务逻辑
    //...


    // 正常返回
    return {
      message: '正常数据返回',
      data: [
        {
          name: 'lee',
        }
      ]
    }
  }
}


```

## Questions & Suggestions

Please open an issue [here](https://github.com/eggjs/egg/issues).

## License

[MIT](LICENSE)
