# Fonksiyonel Programlama Jargonu

## Arity

Bir fonksiyonun aldığı argüman sayısıdır. Bir fonksiyon aldığı argüman sayısına göre _unary_ (1 argüman), _binary_ (2 argüman), _ternary_ (3 argüman)... olarak adlandırılır. Eğer bir fonksiyon değişken sayıda argüman alıyorsa _variadic_ olarak adlandırılır.

```haskell
Prelude> let sum a b = a + b
Prelude> :t sum
sum :: Num a => a -> a -> a

-- sum fonksiyonunun arity'si 2dir.
```

## Higher-Order Functions (HOF)

Argüman olarak bir fonksiyon alan ya da bir fonksiyonu çıktı veren fonksiyonlardır.

```haskell
Prelude> let add3 a = a + 3
Prelude> map add3 [1..4]
[4,5,6,7]
```

```haskell
Prelude> filter (<4) [1..10]
[1,2,3]
```

## Closure

_Kapanış_, bir fonksiyona bağlı değişkenleri koruyan bir kapsamdır.
[Kısmi uygulama](#partial-application) için önemlidir.


```haskell
Prelude> let f x = (\y -> x + y)
```

`f` fonksiyonunu bir sayı ile çağıralım.

```haskell
Prelude> let g = f 5
```

Bu durumda `x = 5` değeri `g` fonksiyonunun kapanışında korunur. Şimdi `g` fonksiyonunu bir `y` değeri ile çağırırsak:

```haskell
Prelude> g 3
8
```

## Partial Application

_Kısmi uygulama_, bir fonksiyonun bazı argümanlarını önceden doldurarak yeni bir fonksiyon oluşturmaktır.

```haskell
-- Orjinal fonksiyonumuz
Prelude> let add3 a b c = a + b + c

--`2` ve `3` argümanlarını `add3` fonksiyonumuza vererek `fivePlus` fonksiyonumuzu oluşturuyoruz
Prelude> let fivePlus = add3 2 3

Prelude> fivePlus 4
9
```

Kısmi uygulama, kompleks fonksiyonlardan daha basit fonksiyonlar oluşturmaya yardım eder. [Curried](#currying) fonksiyonlar otomatik olarak kısmi uygulanmış fonksiyonlardır.

## Currying

Birden çok parametre alan bir fonksiyonu, her defasında sadece bir parametre alan bir fonksiyona dönüştürmektir.

Fonksiyon her çağrıldığında sadece bir argüman kabul eder ve tüm argümanlar verilene kadar sadece bir argüman alan bir fonksiyon döndürür.

```haskell
Prelude> let sum (a, b) = a + b
Prelude> let curriedSum = curry sum
Prelude> curriedSum 40 2
42

Prelude> let add2 = curriedSum 2
Prelude> add2 10
12

```

## Function Composition

İki farklı fonksiyonu bir araya getirerek, bir fonksiyonun çıktısı diğer fonksiyonun girdisi olan üçüncü bir fonksiyon oluşturmaktır.
```haskell
-- fonksiyonları bir araya getirmek için '.' operatörü kullanılır
Prelude> let floorAndToString = show . floor
Prelude> floorAndToString 32.123
"32"
```

## Purity

Bir fonksiyonun çıktısı sadece girdi veya girdilerine bağlı ve fonksiyon yan etki oluşturmuyor ise, fonksiyon _saftır_ denir.

```haskell
Prelude> let greet name = "Hello " ++ name
Prelude> greet "Brianne"
"Hello Brianne"
```

Saf olmayan fonksiyona bir örnek:

```haskell
Prelude> let name1 = "Brianne"
Prelude> let greet = "Hello " ++ name1
Prelude> greet
"Hello Brianne"
```

Yukarıdaki  fonksiyonun çıktısı fonksiyonun dışarısında tanımlı bir değişkene bağlıdır.

## Side effects

Bir fonksiyon veya ifade, dışarısındaki bir durum ile etkileşime geçiyor ise (okuma veya yazma), _yan etki_ ye sahiptir denir.

Haskell'deki tüm fonksiyonlar saftır.

## Idempotent

Bir fonksiyon, sonucuna tekrar uygulandığında sonuç değişmiyorsa _idempotent_ olarak adlandırılır.

```
f(f(x)) ≍ f(x)
```

```haskell
Prelude> abs (abs (-1))
1
```

```haskell
Prelude Data.List> sort (sort [1,4,3,1,5])
[1,1,3,4,5]
```

## Point-Free Style

Argümanların açıkca tanımlanmadığı fonksiyonlar yazmaktır. _Tacit programming_ olarak da bilinir.

```haskell
Prelude> let add a b = a + b

-- incrementAll fonksiyonunu tanımlayalım

-- Point-free değildir - `numbers` argümanı belirtilmiştir
Prelude> let incrementAll numbers = map (+1) numbers

-- Point-free - Fonksiyonun aldığı argüman açıkca belirtilmemiştir
Prelude> let incrementAll = map (+1)
```

`incrementAll` fonksiyonunun `numbers` argümanını aldığı belirtilmiştir, bu nedenle point-free değildir.  `incrementAll2` fonksiyonu ise, fonksiyon ve değerlerin bir bileşimidir ve argüman bilgisi belirtilmemiştir.  Yani _point-free_ dir.

## Predicate
Verilen bir değer için doğru veya yanlış değerini dönen fonksiyonlardır. Genellikle _filter_ ile beraber kullanılırlar.

```haskell
Prelude> let predicate a = a < 3
Prelude> filter predicate [1..10]
[1,2]
```

## Category

Kategory teorisinde bir _kategori_, matematiksel yapılar ve bunlar arasında tanımlı morfizmlerden oluşur. Programlamada ise, tipler matematiksel yapılar, fonksiyonlar ise morfizmlerdir.

Bir kategori aşağıdaki üç koşulu sağlar:

1. Bir yapıyı kendisine eşleyen bir birim morfizm vardır. Yani `a` herhangi bir kategori içinde bir yapı ise, `a -> a` şeklinde tanımlı bir fonksiyon vardır.
2. `a`, `b` ve `c` herhangi bir kategori içindeki yapılar ve `f : a -> b` ve `g : b -> c` ise `h : a -> c`, `h(x)=(g • f)(x)=g(f(x))` vardır.
3. `f • (g • h)` ve `(f • g) • h` ifadeleri aynıdır.

__Daha Fazla Kaynak__

* [Category Theory for Programmers](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)

## Functor

`map` fonksiyonunu implemente eden bir nesnedir ve aşağıdaki iki özelliği sağlar:

__Identity__
```
object.map(x => x) ≍ object
```

__Composable__

```
object.map(compose(f, g)) ≍ object.map(g).map(f)
```

## Pointed Functor

Her `a` tipi için 

``
of :: a -> F a 
``

ile tanımlı bir `of` fonksiyonuna sahip `F` funktoruna denir.

## Referential Transparency

Bir ifade değeri ile yer değiştirildiğinde programın davranışı değişmiyor ise, ifade _referentially transparent_ olarak adlandırılır.

## Lambda

Anonim (isimsiz) fonksiyonlardır.

``
\x -> x + 1
``

Çoğunlukla yüksek mertebeden fonksiyonlar ile birlikte kullanılırlar.

```haskell
Prelude> map (\x -> x + 1) [1..4]
[2,3,4,5]
```

## Lazy evaluation

_Lazy evaluation_, bir ifadenin, ifade sonucuna ihtiyaç duyulana kadar hesaplanmamasıdır. Böylece, sonsuz listeler gibi yapılar tanımlanabilir.

```haskell
Prelude> let lst0 = [1..]
Prelude> take 5 lst0
[1,2,3,4,5]
```

## Monoid

Bir nesneyi aynı tip başka bir nesne ile birleştiren bir fonksiyona sahip bir objedir.

Basit bir monoid örneği sayıların toplanmasıdır:

```haskell
1 + 1 -- 2
```
Bu durumda sayılar nesneler, `+` operatörü ise fonksiyondur.

Birim eleman olmak zorundadır,

Toplama işleminin birim elemanı `0` dır.

```haskell
1 + 0 -- 2
```

ve geçişkenlik özelliği de gereklidir (associativity):

```haskell
1 + (2 + 3) == (1 + 2) + 3
```

## Monad

[`of`](#pointed-functor) ve `chain` fonksiyonlarına sahip bir nesne _monad_ olarak adlandırılır.

```js
// Implementation
Array.prototype.chain = function (f) {
  return this.reduce((acc, it) => acc.concat(f(it)), [])
}

// Usage
Array.of('cat,dog', 'fish,bird').chain((a) => a.split(',')) // ['cat', 'dog', 'fish', 'bird']

// Contrast to map
Array.of('cat,dog', 'fish,bird').map((a) => a.split(',')) // [['cat', 'dog'], ['fish', 'bird']]
```

`of` fonksiyonu bazı fonksiyonel programlama dillerinde `return` olarak;
`chain` fonksiyonu ise `flatmap` ve `bind` olarak geçmektedir.

## Comonad

`extract` ve `extend` fonksiyonlarına sahip bir nesnedir.

```js
const CoIdentity = (v) => ({
  val: v,
  extract () {
    return this.val
  },
  extend (f) {
    return CoIdentity(f(this))
  }
})
```

Extract fonksiyonu bir değeri funktorun dışına çıkarır.

```js
CoIdentity(1).extract() // 1
```

Extend fonksiyonu comonad üzerinde bir fonksiyon çalıştırır. Fonksiyonun çıktı tipi comonad ile aynıdır.

```js
CoIdentity(1).extend((co) => co.extract() + 1) // CoIdentity(2)
```

## Applicative Functor

_Applicative functor_, `ap` fonksiyonuna sahip bir nesnedir. `ap` fonksiyonu nesnedeki bir fonksiyonu, aynı tipte başka bir nesnedeki bir değere uygular.

```js
// Implementation
Array.prototype.ap = function (xs) {
  return this.reduce((acc, f) => acc.concat(xs.map(f)), [])
}

// Example usage
;[(a) => a + 1].ap([1]) // [2]
```

## Morphism

Bir dönüşüm fonksiyonudur.

### Endomorphism

Girdi ve çıktı tipinin aynı olduğu fonksiyonlardır.

```js
// uppercase :: String -> String
const uppercase = (str) => str.toUpperCase()

// decrement :: Number -> Number
const decrement = (x) => x - 1
```

### Isomorphism

İki farklı tipteki nesne arasındaki bir dönüşüm ve ters dönüşüm çiftidir.

```js
// Providing functions to convert in both directions makes them isomorphic.
const pairToCoords = (pair) => ({x: pair[0], y: pair[1]})

const coordsToPair = (coords) => [coords.x, coords.y]

coordsToPair(pairToCoords([1, 2])) // [1, 2]

pairToCoords(coordsToPair({x: 1, y: 2})) // {x: 1, y: 2}
```

## Setoid

`equals` fonksiyonuna (aynı tipler arasında karşılaştırma yapmaya imkan veren) sahip bir nesnedir.


```js
Array.prototype.equals = function (arr) {
  const len = this.length
  if (len !== arr.length) {
    return false
  }
  for (let i = 0; i < len; i++) {
    if (this[i] !== arr[i]) {
      return false
    }
  }
  return true
}

;[1, 2].equals([1, 2]) // true
;[1, 2].equals([0]) // false
```

## Semigroup

Bir nesneyi aynı tipteki başka bir nesne ile birleştirmeye imkan veren `concat` fonksiyonuna sahip bir nesnedir.

```js
;[1].concat([2]) // [1, 2]
```

## Foldable

bir yapıyı tek bir değere dönüştüren `reduce` fonksiyonuna sahip bir nesnedir.

```js
const sum = (list) => list.reduce((acc, val) => acc + val, 0)
sum([1, 2, 3]) // 6
```

## Algebraic data type

Bileşke veri tipleridir - başka tiplerin bir araya gelmesiyle oluşur. En yaygın cebirsel veri tipleri [_toplamsal tipler_](#sum-type) ve [_çarpımsal tipler_](#product-type)dir.

### Sum type


### Product type


## Option

---

__P.S:__ This repo is successful due to the wonderful [contributions](https://github.com/hemanth/functional-programming-jargon/graphs/contributors)!
