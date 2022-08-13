---
layout: post
title: React 里的订阅模式
---
```javascript
// DataModel.js
class Model {
  constructor() {
    this.listeners = [];
  }
  
  addListener(callbackFunction) {
    const listenerId = Date.now();
    const listener = {
      id: listenerId,
      callback: callbackFunction
    }
    this.listeners.push(listener);
    callbackFunction();
    return listenerId;
  }

  removeListener(listenerId) {
    let idx = this.listeners.findIndex((elem)=>elem.id===listenerId);
    this.listeners.splice(idx, 1);
  }

  notifyListener() {
    for (const tl of this.listeners) {
      tl.callback();
    }
  }
}

```

```javascript
// DataModel.js
class DataModel extends Model {
  constructor() {
    super();
    this.collRef = collection(db, 'todoList');
    this.todoList = [];
  }

  loadInitList = async () => {
    const q = query(this.collRef);
    const querySnapshot = await getDocs(q);
    if (querySnapshot.empty) return;
    let newList = [];
    querySnapshot.docs.forEach((docSnapshot)=>{
      let item = docSnapshot.data();
      item.key = docSnapshot.id;
      newList.push(item);
    });
    this.todoList = newList;
    this.notifyListener();
  }

  getTodoListCopy() {
    return Array.from(this.todoList);
  }
}

let theDataModel = undefined;
export function getDataModel() {
  if (!theDataModel) {
    theDataModel = new DataModel();
  }
  return theDataModel;
}

```

```jsx
// App.js
import React, { useEffect, useState } from 'react';
import { getDataModel } from './DataModel';
import Item from './Item';

function App() {
  const dataModel = getDataModel();
  const [todoList, setTodoList] = useState(dataModel.getTodoListCopy());
  useEffect(() => {
    dataModel.loadInitList();
    const dataModelListenerId = dataModel.addListener(() => {
      setTodoList(dataModel.getTodoListCopy());
    });
    return (() => {
      dataModel.removeListener(dataModelListenerId);
    });
  }, []);
  return (<div>
    {todoList.map((item, i) => <Item key={i}/>)}
  </div>);
}

export default App;

```
