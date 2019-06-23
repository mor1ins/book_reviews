# Effective Modern C++

Мое незнание современных стандартов (теперь я это понимаю ещё сильнее) стало основным двигателем для выбора этой книги в качестве материала для самообразования.

## Вывод типов
Не сверх интересная глава, но полезная. +- все интуитивно понятно с выводом типов, но есть грабли на которые было бы неприятно наступить и полезно о них знать.

## auto
Эта глава вызвала двоякое ощущение. С одной стороны это удобно, с другой стороны что делать если нужно быть  уверенным в выводимом типе? Автор предлагает три способа и сам же говори, что они не работают. В целом надо это использовать там, где тип очевиден, либо по-другому писать не получится (писать руками тип лямбды не самая приятная вещь), но нужно быть крайне осторожным.
Но если хоть где-то использовано auto это начинает принуждать использовать его там, где этого делать уже совершенно не хочется.

**Хорошо**:
```
auto l = [] (auto s) {return "it's lambda"; };
```
не придется тысячи раз писать длиннющие строки из std::function<void... и тд.

**Плохо**:
```
auto i = 1;
```
этот код вызывает только вопросы и никак не способствует уменьшению копирования, как происходит в предыдущем примере.

## Переход к современному c++
Самым полезным из данной главы считаю: using вместо typedef, constexpr и noexcept.

**Хорошо**:
```
using Callback = std::function<void()>; // красиво и поддерживает шаблоны

constexpr long long fact(int num) {
  // function for calculate factorial
}
constexpr long long f = fact(20); // посчитается в момент компиляции
```

**Плохо**:
```
typedef std::function<void()> Callback; // каждый раз забываю порядок
                                        // + шаблоны весьма проблематично добавить

long long fact(int num) {
  // function for calculate factorial
}
long long f = fact(20); // считается в момент выполнения
```

Constexpr кружит голову (особенно в контексте с++14). Константы, функции, даже методы классов могут быть constexpr! Время компиляции за подобную фичу - наименьшая возможная цена и нужно использовать ее по максимуму, но интегрировать это в существующий код не такая простая задача, как и иногда понять, что что-то может быть известно во время компиляции.

## Интеллектуальные указатели
Глава крайне полезна, если этот материал когда-то прошел мимо вас, В ином случае можно пробежаться глазами и идти дальше.

## Rvalue, перемещения, прямая передача
Супер полезно, чтобы исправить заблуждения по поводу rvalue, lvalue, перемещения (тут-то и вылезает noexcept), а так же вникнуть как работает прямая передача. Особенно запомнилась одна из оптимизаций компилятора - RVO, при которой память выделяется сразу в переменной куда будут возвращено значение.  

## lambdas
Раздел маленький, но емкий. Вчитываться со всем усердием, кроме пожалуй последнего раздела. Сомневаюсь, что у кого-то он сейчас вызывает вопросы.

## concurrency
Не самый интересная и информативная глава, как мне показалась. + в моих проектах этого не используется и не планируется, поэтому прошелся весьма бегло. Суть: в общем случае ассинхронность лучше мультипоточности, используйте atomic, если все-таки выбрали потоки.

## Что не вошло в прошлые пункты, но достойно упоминания

- Используйте **volatile** для особых участков памяти. Например, при написании драйверов.
```
volatile register8_t reg = 0; // если не написать volatile, компилятор посчитает
                              // эту строку избыточной, так как значение самой переменной ни разу не
                              // используется до его переопределения

_delay_ms(10);
volatile register8_t reg = 50;

```

- Конструкторы с одним параметром должны быть **explicit**, иначе возможно неожиданное поведение
```
class Array {
  ...
  Array(int i);
  ...
};

...
Array a = 5;  // этот код вызовет конструктор Array
              // explicit же обязывает вызывать его более явно
...
```
- Не нужно пихать std::move куда попало, не для всего он хорош.
- Только nullptr и никаких 0 или NULL
```
// Очевидно, что эти строки пытаются служить одной цели, но
char * str1 = 0;        // можно разыменовать, неопределенное поведение
char * str2 = NULL;     // можно разыменовать, неопределенное поведение
char * str3 = nullptr;  // ошибка!
```