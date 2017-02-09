fbDataBase
====

## Database Ruleファイルの確認
database.rules.json
```javascript
{
  "rules": {
    ".read": "auth != null",
    ".write": "auth != null"
  }
}
```
上記の様に変更して下さい。
auth != null は、ログインしていれば許可するという意味です。

### HTMLファイルの修正
public/index.htmlを開いて、
<section id="main-section">ブロックの末尾に以下を追加します

```html
<h1 class="title"> Database </h1>
<div class="columns">
  <div class="column is-5">
    <input id="text-input" class="input" type="text" placeholder="Text input">
  </div>
  <div class="column is-2">
    <button id="text-submit-button" type="submit" class="button is-primary is-fullwidth">Submit --&gt;</button>
  </div>
  <div class="column is-5">
    <input id="text-result" class="input" type="text" disabled>
  </div>
</div>
```

以下の様になる
```html
<section id="main-section" class="section" style="display:none;">
  <div class="container">
    <div class="content">
      <h1>Hello World</h1>
      〜中略〜
    </div>

    <h1 class="title"> Database </h1>
    <div class="columns">
      <div class="column is-5">
        <input id="text-input" class="input" type="text" placeholder="Text input">
      </div>
      <div class="column is-2">
        <button id="text-submit-button" type="submit" class="button is-primary is-fullwidth">Submit --&gt;</button>
      </div>
      <div class="column is-5">
        <input id="text-result" class="input" type="text" disabled>
      </div>
    </div>
  </div>

</section>
```

### JSファイルの修正
public/js/main.jsを開いて、
以下の様に修正

```javascript
// Main.prototype.initに追加
Main.prototype.init = function() {
  // code someting here

  // database
  this.textInput = document.getElementById("text-input");
  this.textSubmitButton = document.getElementById("text-submit-button");
  this.textResult = document.getElementById("text-result");

  this.db = firebase.database();
  this.textSubmitButton.addEventListener('click', this.textSubmit.bind(this));
  this.db.ref('data').on('value', function(snapshot){
    var val = snapshot.val();
    console.log(val);
    this.textResult.value = val.text;
  }.bind(this));
};
```

```javascript
// Mani.prototype.initの下に以下を追記
Main.prototype.textSubmit = function(){
  var data = {
    text: this.textInput.value
  };
  this.textSubmitButton.classList.add('is-loading');
  this.db.ref('data').update(data).then(function(){
    this.textSubmitButton.classList.remove('is-loading');
  }.bind(this));
};
```


### ブラウザで確認する
http://localhost:5000 を開いてログインする
![ブラウザで確認する](./assets/page.png)

- 左側のText Inputに任意のテキストを入力
- Submitボタンを押す
- 右側に入力したテキストが表示される
