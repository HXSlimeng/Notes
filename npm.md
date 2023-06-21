### 1. package.json

- `main`:别人在导入包的时候默认导入的入口文件 （ESModule）

- `module`:导入包的时候的入口文件（commonJs）node 12版本以上也支持es导入

- `types`:指定类型声名文件的路径

- `files`:指定哪些包被推送到npm,`.npmignore`内的定义优先级高于该配置

- 

  > 可以理解为.npmignore为黑名单，files为白名单，files优先级最高

### 2. npm pack

​	可以在该目录下生成一个zip文件就是publish要发布的包

### 3. pnpm link + （包含package.json文件的文件夹）

​	可以安装测试发布的lib包

### 4. vite-plugin-libcss

在lib模式下开发npm包可以将外部的css打包入一个css文件中并引入