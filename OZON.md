30 мин собес Было 3 задачи прям в этих минутах и вопрос про то какие уязвимости знаешь.

Никаких общих стейтов и мутаций!
```js
// numbers
function one(el) {
	if (!el) return 1
	return el(1)
}
function two() {}
function three() {}
// actions
function plus(b) {
	retun (a) => b + a
} 
function minus() {}
function multiply() {}
function divide() {}
console.log(one() === 1)
const two = two() // 2
cosnt plus = plus(two()) // fn (a) => 2 + a
console.log(one(plus(two())) === 3)
console.log(one(minus(two())) === -1)
console.log(three(multiply(two())) === 6)
console.log(three(multiply(one(minus(two())))) === -3)
```

  (-\_-)
--\|||---
   |||
