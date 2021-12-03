### 临时使用
npm --registry https://registry.npm.taobao.org install express

### 永久使用
npm config set registry https://registry.npm.taobao.org
原来的不再使用（20211202）
npm config set registry https://registry.npmmirror.com

### 配置CNPM
npm走的还是官方的，cnpm走的代理
npm install -g cnpm --registry=https://registry.npm.taobao.org

### 恢复使用
npm config set registry https://registry.npmjs.org

### 验证是否设置成功
npm info express
or
npm config get registry

