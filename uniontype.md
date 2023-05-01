# Union型について

* 「または」を表現するための型
* | を使って書く
  * T | U　
  * 意味：型Tまたは型U

### ユニオン型のプロパティアクセス
* 存在しないかもしれないプロパティに対するアクセスはできない

  * Drinkはコーヒーと水の2種類である場合を想定
  * ユニオン型を用いて示す
    * メリット：TypeScriptコンパイラによる型チェックと受けられる
    * DrinkとはDrink型に当てはまるオブジェクトである
      * Drink型を持つオブジェクトはCoffeeかもしれないし、Waterかもしれない
```TypeScript
type Coffee = {
  price: number
}

type Water = {
  size: string
}

type Drink = Coffee | Water

const TullysCoffee: Drink = {
  price: 150
}

const Ilohas: Drink = {
  size: 'M'
}

function getPrice(drink:Drink) {
  //エラー：Property 'price' does not exist on type 'Drink'.
  //Property 'price' does not exist on type 'Water'.
  return drink.price
}
```
  * drinkはpriceプロパティを持たない可能性があるのでプロパティアクセスは許されない
  * コンパイルエラーとなる



```TypeScript
type Coffee = {
  price: number
  temperature: string
}

type Water = {
  price: string
  size: string
}

type Drink = Coffee | Water

const TullysCoffee:Drink = {
  price: 150,
  temperature: 'hot'
}

const Ilohas: Drink = {
  price: '100円',
  size: 'M'
}

function getPrice(drink:Drink) {
  return drink.price
}
```
* drinkは必ずpriceプロパティを持つのでアクセス可能
* コンパイルエラーは発生しない
  * priceの型はnumber | string型

### ユニオン型とインターセクション型の表裏一体な関係
* 相違点：意味が逆　「または」「かつ」
* 共通点：複数の型を組み合わせて新しい型を作る
* ユニオン型からインターセクション型が作られる場合がある

```TypeScript
type Coffee = {
  price: number
}

type Water = {
  size: string
}

function getPrice(TullysCoffee:Coffee) {
  return TullysCoffee.price
}

function getSize(Ilohas:Water) {
  return Ilohas.size
}
//変数mysteriFuncにgetPriceが入るかgetSizeが入るかは実行しないと分からない
const mysteryFunc = Math.random() < 0.5 ? getPrice : getSize

const TullysCoffee:Coffee = {
  price: 150
}
//コンパイルエラーとなる
mysteryFunc(TullysCoffee)
```
* mysteryFuncはCoffeeを受け取るとは限らないので、関数として呼び出せない
* 条件演算子の結果はTrueの場合とFalseの場合のユニオン型
  * ((TullysCoffee:Coffee)=>number) | ((Ilohas:Water)=>string)
```TypeScript
const katsuraDrink: Coffee & Water = {
  price: 300,
  size: 'L'
}
mysteryFunc(katsuraDrink)
```
* 関数としてmysteryFuncを呼び出すためには、引数の型はインターセクション型とする
