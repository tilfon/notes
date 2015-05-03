Mac OSX 下未使用管理员权限进行 `npm update` 失败后找不到 `npm` 命令的解决方法:

```shell
cd /usr/local/bin && ln -s ../lib/node_modules/npm/bin/npm-cli.js npm
```

**其实就是再手动链一次**