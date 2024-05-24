---
title: JavaScript基礎
author: rauszero
date: 2021-12-24 20:55:00 +0800
categories: [Programming, JavaScript]
tags: [JavaScript]
---

## 変数宣言

#### var
上書き・再宣言可能

#### let
上書き可能 再宣言不可能

#### const
上書き・再宣言不可能

※ 文字列・数値は一度設定した値が変わらないことが保証されるが

JSのオブジェクトや配列に関しては値を変えることができてしまう

constで定義したオブジェクト・配列にはプロパティの変更が可能

## 文字列の連結

#### 従来の連結
```js
const kind = "不可解";
const skill = "魔法";

// 不可解系です 魔法はかけられません
const msg1 = kind + "系です " + skill + "はかけられません";

console.log(msg1);
```

#### テンプレートリテラル
```js
const msg2 = `${kind}系です ${skill}はかけられません`;

console.log(msg2);
```

## アロー関数

#### 従来の関数1
```js
function func0(str) {
  return str;
}

console.log(func0("func0です"));
```

#### 従来の関数2
```js
const func1 = function(str) {
  return str;
}

console.log(func1("func1です"));
```

#### アロー関数
```js
const func2 = (str) => {
  return str;
}

console.log(func2("func2です"));
```

#### アロー関数 省略記法
```js
const func3 = str => str;

console.log(func3("func3です"));
```

処理が一行で終わってそれを返却するような時は波括弧やreturnの記述を省略することができる

```js
const func4 = (num1, num2) => {
  return num1 % num2;
}

console.log(func4(3, 6));

const func5 = (num1, num2) => num1 % num2;

console.log(func5(6, 9));
```

## 分割代入
```js
const myProfileObj = {
  kind: "不可解",
  skill: "魔法",
};

// オブジェクトから要素を抜き出す
const msg2 = `${myProfileObj.kind}系です ${myProfileObj.skill}はかけられません`;

console.log(msg2);

// 分割代入
const { kind, skill } = myProfileObj;

const msg3 = `${kind}系です ${skill}はかけられません`;

console.log(msg3);

// 配列から要素を抜き出す
const myProfileArray = ["ドラクエ", "呪文"];

const msg4 = `${myProfileArray[0]}系です ${myProfileArray[1]}はかけられません`;

console.log(msg4);

// 配列で分割代入を用いる際は順番に注意
const [kind2, skill2] = myProfileArray;

const msg5 = `${kind2}系です ${skill2}はかけられません`;

console.log(msg5);
```

## デフォルト値
```js
// デフォルト値未設定
const sayHello = (name) => console.log(`Hello!${name}!`);

// Hello!raus!
sayHello("raus");

// Hello!undefined!
sayHello();

// デフォルト値設定
const sayHello2 = (name = "raus") => console.log(`Hello!${name}!`);

// Hello!raus!
sayHello2();
```

## スプレッド構文
```js
// 配列の展開
const arr1 = [1, 2];

// [1, 2]
console.log(arr1);

//　配列の中身を順番に処理して展開 1 2
console.log(...arr1);

// 例
const sumFunc = (num1,num2) => console.log(num1 + num2);
// 3
sumFunc(arr1[0], arr1[1]);
// 3
sumFunc(...arr1);
```

```js
// まとめる
const arr2 = [1, 2, 3, 4];
const [num1, num2, ...arr3] = arr2;

console.log(num1); // 1
console.log(num2); // 2
console.log(arr3); // [3, 4]
```

※ この方法で配列をコピーすることにより 参照を引き継がないので arr6に操作を加えても 元の配列に影響を与えない

```js
const arr4 = [10, 20];
const arr5 = [30, 40];

const arr6 = [...arr4];
arr6[0] = 100;
console.log(arr6); // [100, 20]

const arr7 = [...arr4, ...arr5];
console.log(arr7); // [10, 20, 30, 40]
```

※ =で配列をコピーしてしまうと結果的に同じところを参照してしまうため arr8に操作を加えると 元の配列が影響を受けてしまい不具合の元となる動きをしてしまう

```js
const arr4 = [10, 20];

const arr8 = arr4;
arr8[0] = 100;
console.log(arr8); // [100, 20]
console.log(arr4); // [100, 20]
```

## mapやfilterを使った配列の処理
#### 従来のfor文による処理
```js
const categoryArr = ["ABOUT", "BLOG", "CODE"];

for (let index = 0; index < categoryArr.length; index++) {
  console.log(categoryArr[index]);
}
```

#### mapによる処理
・配列をループして処理をする

・戻り値に基づいて新しい配列を作成

```js
const categoryArr = ["ABOUT", "BLOG", "CODE"];

const categoryArr2 = categoryArr.map((name) => {
	return name;
})

console.log(categoryArr2);
```

#### 省略記法
```js
const categoryArr = ["ABOUT", "BLOG", "CODE"];

categoryArr.map((name) => console.log(name));

// 何番目か出す
categoryArr.map((name, index) => console.log(`${index + 1}番目は${name}です`));
```

#### filterによる処理
・配列から条件に一致するものだけ抽出

```js
const numArr = [1, 2, 3, 4, 5];

const newNumArr = numArr.filter((num) => {
	return num % 2 === 1; // 奇数
});

console.log(newNumArr);
```

## 三項演算子
```js
const val1 = 1 > 0 ? `trueです` : `falseです`;

console.log(val1); // trueです
```

```js
const num = 1300;

console.log(num.toLocaleString()); // 数値のみ
```

```js
const num = "1300";

const formattedNum = typeof num === 'number' ? num.toLocaleString() : '数値を入力してください';

console.log(formattedNum); // 数値を入力してください
```

```js
const checkSum = (num1, num2) => {
	return num1 + num2 > 100 ? '100を超えています！！' : '許容範囲内です';
}

console.log(checkSum(50, 40)); // 許容範囲内です
```

## 論理演算子の本当の意味
```js
const flag1 = true;
const flag2 = false;

// ×　または
if (flag1 || flag2) {
	console.log("1か2かはtrueになります");
}

// ×　かつ
if (flag1 && flag2) {
	console.log("1か2かはtrueになります");
}
```

#### 左がfalseなら右を返す
```js
const num = null;
const fee = num || "金額未設定です";

console.log(fee); // 金額未設定です
```

#### 左がtrueなら左を返す
```js
const num = 100;
const fee = num || "金額未設定です";

console.log(fee); // 100
```

#### 左がtrueなら右を返す
```js
const num = 100;
const fee = num && "何か設定されました";

console.log(fee); // 100
```

#### 左がfalseなら左を返す
```js
const num = null;
const fee = num && "何か設定されました";

console.log(fee); // 100
```
