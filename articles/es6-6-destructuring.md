# ES6: реструктуризующее присваивание

Скорее всего, вы уже видели несколько примеров использования реструктуризующего присваивания, или *destructuring*. Данная синтаксическая конструкция используется для извлечения данных из массивов и объектов. 

Чтобы вас не смущало такое хитрое название, можно представить процесс, не как *разложение* объекта или массива на его составляющие, а как простое *структурированное* присваивание. Проще всего понять новую синтаксическю конструкцию можно, рассмотрев пример из прошлого:
```javascript
// Структурированное присваивание для массивов
var arr = ['this', 'is', 'array'];
var first = arr[0], 
    second = arr[1], 
    third = arr[2];

console.log(first, second, third); // this is array

// Структурированное присваивание для объектов
var obj = {
  a: 'this',
  b: 'is',
  c: 'object'
};
var a = obj.a, 
    b = obj.b, 
    c = obj.c;

console.log(a, b, c); // this is object
``` 

Проще говоря, раньше приходилось записывать каждую переменную по отдельности вручную. А если приходилось иметь дело с динамическими данными, то могла понадобиться и ещё одна дополнительная переменная:
```javascript
var f = function() {
  return ['this', 'is', 'array'];
};

var tmp = f(),
    first = tmp[0], 
    second = tmp[1], 
    third = tmp[2];

console.log(first, second, third); // this is array
``` 

С релизом ES6 всё стало в разы проще:
```javascript
var f = function() {
  return ['this', 'is', 'array'];
};

// ES6 destructuring для массивов
var [ first, second, third ] = f();

console.log(first, second, third); // this is array
```

## Destructuring с объектами
Создание нескольких переменных одновременно из объекта настолько же простое, как и из массива. Если вы хотите создать переменную с тем же именем, что и свойство объекта, то можно использовать сокращенную конструкцию:
```javascript
var obj = {
  a: 'this',
  b: 'is',
  c: 'object'
};
var { a, b, c } = obj;

console.log(a, b, c); // this is object
```

В другом случае, когда имя переменной отличается от свойства, используется полный вариант:
```javascript
var obj = {
  a: 'this',
  b: 'is',
  c: 'object'
};

var { a: x, b: y, c: z } = obj;

// Данные записаны в переменные x, y и z
console.log(x, y, z); // this is object

// Переменных a, b, c не существует
console.log(a, b, c); // ReferenceError
```

Важно понимать отличие *реструктуризующего присваивания* от создания объекта с помощью литерала:
```javascript
// Создание объекта 
var a = 10, b = 20;
var obj = { x: a, y: b };
console.log(obj.x, obj.y); // 10 20
```

Создавая объект с помощью литерала, вы следуете правилу: справа находится свойство, слева − значение `{ свойство: значение }`. Подобное присваивание свойств интуитивно понятно, так как можно представить его, как обычное присваивание значение переменной `переменная = значение`. 

Однако, когда вы используете *реструктуризующее присваивание*, названное выше правило действует в точности наоборот `{ значение: свойство }`. 
```javascript
var a = 10, b = 20;
var obj = { x: a, y: b };
var       { x: A, y: B } = obj;
console.log(A, B); // 10 20
```

В случае `var obj = { x: a, y: b }` x и y представляют свойства объекта. В случае `var { x: A, y: B } = obj;` x и y **тоже** представляют свойства. 

### Присваивание при отсутствии свойства
Если переданного вами свойства нет в объекте, то вы получите переменную, содержащую `undefined`:
```javascript
var obj = { a: 1 };
// у объекта нет свойства b
var { x: a, y: b } = obj;
// переменной y будет присвоено undefined
console.log(x, y); // 1 undefined
```

### Вложенность
*Реструктуризующее присваивание* можно использовать с любым уровнем вложенности:
```javascript
var o = { zero: [{ one: 1, two: 2 }, { three: 3, four: 4 } ], five: 5 };
var { zero: [{ one: first }], five: fifth } = o;

console.log(first, fifth); // 1 5
```

### Обращение к свойствам примитивов
Примитивные значения не являются объектами, но обладают свойствами и методами, которые можно получить с помощью *реструктуризующего присваивания*:
```javascript
var { length: l, trim: t } = 'this is string';
console.log(l); // 14
console.log(t); // String.prototype.trim
```

## Destructuring с массивами
Выше я уже рассматривал пример применения *destructuring* с массивами. В целом, применение достаточно схоже с объектами, за исключением двух ключевых особенностей:

##### Работает с любыми коллекциями
```javascript
// Строки
var [a, b, c] = 'xyz';
console.log(a, b, b); // x y z

// Коллекции DOM элементов
// Первые две ссылки со страницы
var [link1, link2] = document.links;
console.log(link1.tagName); // A
console.log(link2.textContent); // текстовое содержание ссылки
```

##### Можно использовать [оператор spread](http://jsraccoon.ru/es6-spread-rest/) при присваивании
```javascript
var [a, b, ...c] = [1, 2, 3, 4, 5];
console.log(a, b, c); // 1 2 [3, 4, 5]
```
В данном случае переменные a и b получат соответственно значения `1` и `2`, а переменная c все оставшиеся значения в виде массива `[3, 4, 5]`.

## Параметры по умолчанию
При использовании *реструктуризующего присваивания* можно задать [параметры по умолчанию](http://jsraccoon.ru/es6-defaults/), на случай, если массив или объект не содержат присваиваемого свойства:
```javascript
// Без параметра по умолчанию
var [x, y, z] = [1, 2];
console.log(x, y, z); // 1 2 undefined

// С использованием параметра по умолчанию
var [x, y, z = 3] = [1, 2];
console.log(x, y, z); // 1 2 3
```

Также, как и в случае с функциями, в качестве параметра по умолчанию можно передать выполнение какой-либо функции:
```javascript
var calc = function(num) {
  return Math.pow(num, 2);
};

var { prop: x, pow: y = calc(x)} = { prop: 4 };
console.log(x, y); // 4 16

var { prop: x, pow: y = calc(x)} = { prop: 4, pow: 10 };
console.log(x, y); // 4 10
```