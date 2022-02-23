### 报错
```ts
import 'logo.svg'
```

### 解决
1. 新建文件：svg.d.ts
```ts
declare module '*.svg' {

const content: React.FunctionComponent<React.SVGAttributes<SVGElement>>;

export default content;

}
```
2. 在tsconfig.json中添加
```json
"include": ["src/svg.d.ts"]
```