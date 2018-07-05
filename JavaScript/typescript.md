## 数据类型

```ts
let visible: boolean　= false;
let arr: number[] = [1]; // let arr: Array<number> = [1];
let tuple: [string, number] = ['hello', 0]; // 越界元素将用联合类型替代, 例如变量`tuple`便可赋值(string | number)类型

enum Color {Red, Green, Blue} // enum Color {Red = 2, Green, Blue}
let c: Color = Color.Green;

let list: any[] = [1, true, 'free'];
let notSure: any = 4;
notSure = false;

// Type assertions
let someValue: any = 'this is a string';
let strLength: number = (someValue as string).length;
let strLength: number = (<string>someValue).length;
```

## 函数与接口

```ts
function add(x: number, y: number): number {
  return x + y;
}

function print(obj: { label: string }) {
  console.log(obj.label);
}


interface Obj {
  label: string;
  width?: number;
}
function create(obj: Obj): { label: string } {
  return { label: obj.label };
}
// 绕开额外属性检查(excess property checking)
create({ label: 'a', w: 1 } as Obj);
const obj = { label: 'b', w: 2 };
create(obj); // obj不会经过额外属性检查


// 函数类型的接口
interface MathFunc {
  (x: number, y: number): number;
}
const add: MathFunc;
add = function(a, b) {
  return a + b;
}
// 混合类型的接口
interface Counter {
  (start: number): string;
  interval: number;
  reset(): void;
}

let deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  createCardPicker: function() {
    return () => {
      // ERROR this in this.suits[0] is of type any
      return {suit: this.suits[0]};
    };
  },
}
interface Card {
  suit: string;
}
interface Deck {
  suits: string[];
  createCardPicker(this: Deck): () => Card;
}
let deck: Deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  createCardPicker: function(this: Deck) {
    return () => {
      return {suit: this.suits[0]};
    };
  },
}
