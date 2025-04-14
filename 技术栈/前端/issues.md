# 无错误提示

vue元素绑定了未定义的事件，会有问题但不会有提示。

# npm i 安装依赖卡住

--verbose查看输出信息

```bash
npm install --verbose
```

更换镜像源

```bash
npm config set registry=https://registry.npmmirror.com 
 
//执行以下命令查看是否配置成功
npm config get registry
```

