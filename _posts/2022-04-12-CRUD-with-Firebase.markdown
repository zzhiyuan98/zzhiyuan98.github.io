---
layout: post
title: 用 Firebase 实现增删改查
---

```react
// DataModel.js
class DataModel extends Model {
  constructor() {
    super();
    this.collRef = collection(db, 'todoList');
    this.todoList = [];
  }

  ...

  addItem = async (item) => {
    let docRef = await addDoc(this.collRef, item);
    item.key = docRef.id;
    this.todoList.push(item);
    this.notifyListener();
  }

  deleteItem = async (key) => {
    const docRef = doc(db, 'todoList', key);
    await deleteDoc(docRef);
    let idx = this.todoList.findIndex((elem)=>elem.key===key);
    this.todoList.splice(idx, 1);
    this.notifyListener();
  }

  updateItem = async (key, newItem) => {
    const docRef = doc(db, 'todoList', key);
    await updateDoc(docRef, newItem);
    let idx = this.todoList.findIndex((elem)=>elem.key===key);
    this.todoList[idx] = newItem;
    this.notifyListener();
  }

  getItem(key) {
    let idx = this.todoList.findIndex((elem)=>elem.key===item.key);
    return(this.todoList[key]);
  }

}

```