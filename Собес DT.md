# План 
1. Как бы ты организовал код в проекте
	+ если FSD то по фсд
	+ Какие правила были если не fsd?
2. Алгоритмическая задачка
3. Debounce в 3 этапа
# Нечеткий поиск в строке йопт 
##### Вопрос
```js
//Нужно найти нечеткую подстроку в заданной строке и вернуть true, если она там есть, иначе false.
//Строка A является нечеткой подстрокой строки B если A содержится как подпоследовательность в строке B
//подпоследовательность это такая последовательность полученная путем удаление некоторого количество символов(возможно 0) из начальной строки 

console.log(fuzzySearch('car', 'cartwheel')) // True
console.log(fuzzySearch('cwhl', 'cartwheel')) // True
console.log(fuzzySearch('cwhee', 'cartwheel')) // True
console.log(fuzzySearch('cartwheel', 'cartwheel')) // True
console.log(fuzzySearch('cwheeel', 'cartwheel')) // False
console.log(fuzzySearch('lw', 'cartwheel')) // False 
```
##### Ответ
```js
function fuzzySearch(sub, string) {
    let sub_i = 0;
    let string_i = 0;

    while (sub_i < sub.length) {
        while (string_i < string.length) {
            if (sub[sub_i] === string[string_i]) {
                sub_i++;
                string_i++;
                break;
            } else {
                string_i++;
            }
        }
        
        if (string_i === string.length && sub_i < sub.length) {
            return false;
        }
    }

    return true;
}
```

# Debounce 
## Этап 1

##### Вопрос
```js 
// Написать функцию Debounce, которая будет задерживать вызов фукнции до тех пор,
// пока не пройдет указанное время с последнего вызова функции
function search(a, b) {
  console.log('We are searching ', a, b)
}

const debouncedSearch = debounce(search, 100)
debouncedSearch('1') // отмена
debouncedSearch('2') // отмена
debouncedSearch('3') // вызов спустя n милисекунд
```
##### Ответ 
```js 
function debounce(func, ms) {
  let timeout;
  return function(...args) {
      clearTimeout(timeout);
      timeout = setTimeout(() => func(...args), ms);
  };
}
```
## Этап 2
##### Вопрос 
```js 
// Написать функцию Debounce, которая будет задерживать вызов фукнции до тех пор,
// пока не пройдет указанное время с последнего вызова функции
function search(a, b) {
  console.log('We are searching ', a, b)
}
const debouncedSearch = debounce(search, 100)
debouncedSearch('1') // отмена
debouncedSearch('2') // отмена
debouncedSearch('search string').then((result) => console.log(result, 'its result')) // вызов спустя n милисекунд


const user = {
  name: 'Paul',
  logName: debounce(function() {
    console.log(this.name)
  }, 1000)
}
user.logName()
```
##### Ответ

```js
function debounce(func, ms) { 
let timeout;
  return function(...args) {
      clearTimeout(timeout);
      timeout = setTimeout(() => resolve(func.apply(this, args)), ms);
  };
}
```

## Этап 3
##### Вопрос 
```js 
// Написать функцию Debounce, которая будет задерживать вызов фукнции до тех пор,
// пока не пройдет указанное время с последнего вызова функции
function search(a, b) {
  console.log('We are searching ', a, b)
  return a
}


const debouncedSearch = debounce(search, 100)

debouncedSearch('1') // отмена
debouncedSearch('2') // отмена
debouncedSearch('search string').then((result) => console.log(result, 'its result')) // вызов спустя n милисекунд
// Пример асинхронного вызова с получением результата
async function test() {
  const result = await debouncedSearch('search string')
}

const user = {
  name: 'Paul',
  logName: debounce(function() {
    console.log(this.name)
  }, 1000)
}
user.logName()
```
##### Ответ 
```js 
function debounce(func, ms) {
  let timeout;
  return function(...args) {
    return new Promise((resolve, reject) => {
      clearTimeout(timeout);
      timeout = setTimeout(() => resolve(func.apply(this, args)), ms);
    })
  };
 **** 
}
```

# Доп инфа 
Сайт для ревью: https://app.coderpad.io/dashboard/pads
HR для связи: Anastasia Batina 