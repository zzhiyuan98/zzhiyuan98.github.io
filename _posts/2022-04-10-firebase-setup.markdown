---
layout: post
title: Firebase✖️React联名：解放前端的后端
---
### Firebase是什么？
- Google提供的一种后端解决方案——“不需要服务器的后端”
- NoSQL的数据库，弹性化拉满
- 非常适合我这种(目前)还不太会后端的小白

### Firestore数据库长什么样？
- 在最上面一层，只有集合(collections)，集合可以想成是SQL数据库里的Table
- 集合只包含文档(documents)，文档可以想成是Table里面的每一行
- 文档可以包含fields或者集合，fields类似于Table里面的列，值得注意的是，文档还可以嵌套集合
- fields拥有不同的数据类型(string, number, boolean, array,...)
- Sibling collections(最高层的集合或同一个文档下的集合)必须拥有独一无二的ID(名字)
- Sibling documents(同一个集合下的文档)必须拥有独一无二的ID
- 文档的ID可以自动生成且通常都是自动生成，集合则不然，通常会人为命名集合
- 如果你了解JSON的话，Firestore的结构有点类似于JSON objects的结构，简单来说就是集合套文档，文档套集合

### 如何配置？
#### 创建一个Firebase项目和Firestore数据库
1. [console.firebase.google.com] -> Add project
![截图](/assets/blog%232/console.png)
2. 取名，需要在整个Firebase系统里独一无二 -> Continue
![截图](/assets/blog%232/create1.png)
3. 关掉Google Analytics -> Create project
![截图](/assets/blog%232/create2.png)
4. 等候一段时间 -> Continue
![截图](/assets/blog%232/ready.png)
5. 跳转以后在左侧边栏找到被高亮的图标并点击
![截图](/assets/blog%232/sidebar.png)
6. Create database -> 选择test mode -> Continue
![截图](/assets/blog%232/test-mode.png)
7. 选择一个离你比较近的地域作为数据库的位置 -> Enable
![截图](/assets/blog%232/location.png)
8. 完成～我们来新建一个集合看看 -> Start collection -> 为集合取一个名字(ID) -> Next
![截图](/assets/blog%232/coll.png)
9. 新建一个文档 -> Auto-ID自动生成文档ID -> Save
![截图](/assets/blog%232/doc.png)
10. 完成
![截图](/assets/blog%232/done.png)

#### 创建一个Firebase app
1. 在左侧边栏找到home图标并点击
![截图](/assets/blog%232/home.png)
2. 选择Web app -> 取名 -> Register app
![截图](/assets/blog%232/add.png)
3. 完成
![截图](/assets/blog%232/sdk.jpg)

#### 在代码中使用
```javascript
// Secrets.js
const firebaseConfig = {
  apiKey: "XXXXXXXXXXXXXXXXXXXXX",
  authDomain: "YOUR-PROJECT-ID.firebaseapp.com",
  projectId: "YOUR-PROJECT-ID",
  storageBucket: "YOUR-PROJECT-ID.appspot.com",
  messagingSenderId: "XXXXXXXXXXX",
  appId: "X:XXXXXXXX:web:XXXXXXXXXXX"
};
export default firebaseConfig;
```

```
# .gitignore
node_modules/**/*
Secrets.js

# Annoying Mac OS file
.DS_Store
```


```javascript
// DataModel.js
import { firebaseConfig } from './Secrets';
import { initializeApp, getApps } from 'firebase/app';
import { 
  getFirestore, collection, query, doc, 
  getDoc, getDocs, setDoc, addDoc, deleteDoc, updateDoc
} from "firebase/firestore";

let app;
if (getApps().length === 0){
  app = initializeApp(firebaseConfig);
}
const db = getFirestore(app);

```


[console.firebase.google.com]: https://console.firebase.google.com
