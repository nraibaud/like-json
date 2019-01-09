# like-json

![](https://vgy.me/CgirYE.png)
Benchmark it yourself: https://jsperf.com/like-json

## Install
```
npm i like-json
```

## Examples
If you want to start using like-json:
```javascript
console.log(JSON.stringify({ msg: 'Created.' }));
//to ->
console.log(like.stringify({ msg: 'Created.' }, 1)); //where 1 is a unique id for this struct
```

After that, can get more performance if you avoid the internal if and function call in .stringify:
```javascript
//on somewhere
let stringify = like.json({ msg: '' });

//and use it
console.log(stringify({ msg: 'Created.' }, 1));
```

## How it works?
Basically, almost there is no processing, so same than a simple concatenation
```javascript
console.log(stringify.toString()); //see the internal code with the previous example
```

## Tests
```javascript
let like = require('like-json');

let ex = [
  'a', 1, 1.0, true, false, null, undefined, {}, [],
  Infinity, NaN, Date, String, Number, Object, Array, function() {}, Symbol,
  new String('aaaa'), new Number(1), new Boolean(false), new Date(),
  new Set([1]), new Map([[1, 1]]), new WeakSet([{ a: 1 }]), new WeakMap([[{ a: 1 }, 1]]),
  new Int8Array([1]), new Int16Array([1]), new Int32Array([1]),
  { [Symbol.for('aaa')]: 'aaa' }, Symbol.for('aaa'),
  Object.create(null, { x: { value: 'x', enumerable: false }, y: { value: 'y', enumerable: true } }),
  'a\'"a\\a\"a', 'Iñtërnâtiônàlizætiøn', 
  { toJSON: function() { return 'aaa'; } },

  //'It\'s ok \1 ay', //this fails because I'm not sure how encode \1, \4, etc properly and optimized way
  //{ toJSON: function() { return '"aaa"'; } }, //these two fails because toJSON behaviour is very simple
  //{ toJSON: function() { return '{"a":"b"}'; } },
  //{ toJSON: function() { return 111; } } //'111'
];

ex.a = 1; //set k/v on array

//"fails" means that no return the same JSON than JSON.stringify

for(let i in ex) {
  check(ex[i]);
  check([ex[i]]);
  check({ a: ex[i] });
}

console.log('checks finished');

function check(data, i) {
  let expect = JSON.stringify(data);
  let result = like.stringify(data, Math.random(), { encode: true, finite: true });

  if(expect !== result) {
    console.log('fails');
    console.log('intern', data);
    console.log('expect', expect);
    console.log('result', result);
    console.log('-----');
  }
}

```