# Firebase Intro

![](http://esketchers.com/wp-content/uploads/2016/10/image00.png)

## 介紹

那麼多資料庫，為什麼選擇 Firebase ? 可用 JavaScript 操控資料

Firebase 需綁定 google 帳號

## 環境講解

android、ios 都可透過 frebase 當作資料庫、會員驗證機制

## 環境設定

1. 到 總覽 > 將 Firebase 加入您的網路應用程式 > 程式碼片段 進行複製
2. 貼到 HTML `<head>` 標籤內最底下

    ```html
    <!doctype html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <meta name="viewport"
            content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>Document</title>
      <script src="https://www.gstatic.com/firebasejs/4.9.0/firebase.js"></script>
      <script>
        // Initialize Firebase
        var config = {
          apiKey: "AIzaSyCzhlZyXgSjY-M2cw77oVat8BRT3gnglus",
          authDomain: "project-29c37.firebaseapp.com",
          databaseURL: "https://project-29c37.firebaseio.com",
          projectId: "project-29c37",
          storageBucket: "project-29c37.appspot.com",
          messagingSenderId: "231919916628"
        };
        firebase.initializeApp(config);
      </script>
    </head>
    <body>
    <h1>hello</h1>
    </body>
    </html>
    ```

3. 加入下列程式碼，確認是否可正確連接

    ```javascript
    var database = firebase.database();
    console.log(database);
    ```

## ref (路徑)、set (新增)

- ref()：尋找資料庫路徑
- set()：新增資料的語法

```javascript
firebase.database().ref().set('hi'); // ref(): 代表根目錄
```

會跳出

```
FIREBASE WARNING: set at / failed: permission_denied 
Uncaught (in promise) Error: PERMISSION_DENIED: Permission denied
```

錯誤，firebase 預設不會讓你可以寫入資料

請對 Firebase 主控台“規則”頁籤暫時調整 (實務上不是這樣)：

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

再按發佈，會有“您的安全性規則定義成公開狀態，因此任何人都能讀取或寫入您的資料庫”先不管它

console 下也可新增資料 (物件)：

```javascript
firebase.database().ref().set({home:'US', home2:'TW'});
```

這樣就會有兩筆資料了

---

Firebase 全部物件格式，不能陣列內容！

一個班級有兩個學生：

```javascript
var data = null;

data = {
  student1: {
    name: 'Tom',
    num: '1'
  },
  student2: {
    name: 'John',
    num: '2'
  }
};
fireabase.database().ref().set(data);
```

改部分欄位資料？

```javascript
firebase.database().ref('student1/name').set('Bob');
```

---

多層次資料結構？ 以餐廳為例：

```javascript
var data = null;

data = {
  food: {
    coke: {
      price: 30,
      num: 1
    },
    fries:{
      price: 50,
      num: 20
    }
  },
  order: {
    1: {
      coke: 2
    },
    2: {
      fries: 5,
      coke: 2
    }
  }
};
```

盡量扁平，不要越寫越進去。

```javascript
firebase.database().ref('food/coke/price').set(100);
```

## once - 顯示資料到網頁上

```javascript
// firebase.database().ref('myName').set('mark');
var myName = firebase.database().ref('myName');
// once 讀取一次資料庫的資料、on 是隨時監聽
// shapshot: 快照
myName.once('value', function (snapshot) {
  console.log(snapshot.val());
});
```

```javascript
// firebase.database().ref('myFriends').set({friend1: 'tom', frient2: 'bob'});
var frientsRef = firebase.database().ref('myFriends');
frientsRef.once('value', function (snapshot) {
  console.log(typeof(snapshot.val()));
});
```

## on - 資料即時呈現

```javascript
// on 隨時監聽
  var nameRef = firebase.database().ref('myName');
  nameRef.set('mark');
  nameRef.on('value', function (snapshot) {
    document.getElementById('title').textContent = snapshot.val();
  });
```

然後，至 Firebase 調整資料，會隨時在畫面上反應調整後的結果。

適合做聊天室。

## Firebase 非同步觀念

Firebase 是非同步的。

```javascript
var myName = firebase.database().ref('myName');
myName.once('value', function (snapshot) {
  console.log(snapshot.val());
});
console.log('qq'); // 會先出現
```

撈回資料是需要時間的。

## push - 新增資料

```javascript
// push 是 push 物件進去
  // todolist
  // key: value
  var todos = firebase.database().ref('todos');
  todos.push({content: '今天要記得刷牙'});
  todos.push({content: '今天要記得洗澡'});

  // 等同：
  var data = {
    todos: {
      '-L4-ehDz2zQGqUMSAXsP': {
        content: '今天要記得刷牙'
      },
      '-L4-ehE0m4LZsg6FC-pW': {
        content: '今天要記得洗澡'
      },
    }
  }
```

## remove、child - 移除資料

```javascript
// push 是 push 物件進去
  // todolist
  // key: value
  // child 當下子路徑、remove()
  // var todos = firebase.database().ref('todos');
  var todos = firebase.database().ref().child('todos'); // 同上，這個做法等一下會用到
  todos.child('-L4-ehDz2zQGqUMSAXsP').remove();
```

## for-in 語法講解

```javascript
// for in
// for (variable in [object | array]) {
//   statements
// }

// 陣列的話變數會依序撈出 索引
var colors = ['red', 'black', 'yellow'];
var kaohsiung = [
    {
      father: 'tom',
      mom: 'mary'
    },
    {
      father: 'bob',
      mom: 'jane'
    }
];
// 物件的話變數會依序撈出 屬性
var todos = {
  num1: {
    content: '要記得刷牙'
  },
  num2: {
    content: '要記得洗澡'
  }
}
```

## 網頁上即時瀏覽 Firebase 資料

```javascript
var ref = firebase.database().ref();
ref.on('value', function (snapshot) {
  document.getElementById('content').textContent =
      JSON.stringify(snapshot.val(), null, 3); // 3 代表縮排的項目
});
```

底下放一個即時瀏覽 Firebase 資料庫，可以用來除錯 (路徑隨你設置，不一定都是根目錄)。

## 新增一個 todolist

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport"
        content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
<h1>TodoList</h1>
<input id="txt" type="text" placeholder="請輸入內容...">
<input id="send" type="button" value="送出">
<ul id="list">
</ul>
<script src="https://www.gstatic.com/firebasejs/4.9.0/firebase.js"></script>
<script>
  // Initialize Firebase
  var config = {
    apiKey: "AIzaSyCzhlZyXgSjY-M2cw77oVat8BRT3gnglus",
    authDomain: "project-29c37.firebaseapp.com",
    databaseURL: "https://project-29c37.firebaseio.com",
    projectId: "project-29c37",
    storageBucket: "project-29c37.appspot.com",
    messagingSenderId: "231919916628"
  };
  firebase.initializeApp(config);

  // dom
  var txt = document.getElementById('txt');
  var send = document.getElementById('send');
  var list = document.getElementById('list');

  // todos
  var todos = firebase.database().ref('todos');

  // 按送出按鈕，可以寫入到資料庫
  send.addEventListener('click', function (e) {
    todos.push({content: txt.value});
  });

  // 顯示內容出來
  todos.on('value', function (snapshot) {
    var str = '';
    var data = snapshot.val();
    for (var item in data) {
      str += '<li>' + data[item].content + '</li>';
    }
    list.innerHTML = str;
  });
  
  // 
</script>
</body>
</html>
```

---

增加刪除邏輯：

```javascript
// ...
// 顯示內容出來
todos.on('value', function (snapshot) {
  var str = '';
  var data = snapshot.val();
  for (var item in data) {
    str += '<li data-key="' + item + '">' + data[item].content + '</li>';
  }
  list.innerHTML = str;
});

// 刪除邏輯
list.addEventListener('click', function (e) {
  if (e.target.nodeName = 'LI') {
    var key = e.target.dataset.key;
    todos.child(key).remove();
  }
});
```