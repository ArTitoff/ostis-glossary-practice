# **ScTemplate API**

!!! Предупреждение
    Эта документация верна только для версий sc-machine, которые >= 0.10.0.
---

Sc-шаблоны - это очень мощный механизм для работы с семантической сетью (графом). С помощью sc-шаблонов вы можете генерировать любые конструкции и выполнять поиск по ним. На следующем рисунке показан sc-шаблон и изоморфная sc-конструкция.

<scg src="../images/templates/template_example_1.gwf"></scg>

!!! примечание
    Чтобы подключить этот API, укажите #include <sc-memory/sc_memory.hpp> в вашем исходном файле hpp.

## **ScTemplate**

Класс для работы с sc-шаблонами в C++ API. Перед прочтением этого абзаца вам необходимо ознакомиться с общей информацией о типах sc-элементов. **ССЫЛКА НА ФАЙЛ**

Будем использовать символы `f` для обозначения постоянного параметра sc-шаблона. Будем использовать символ `a` для обозначения переменного параметра sc-шаблона. Тогда sc-шаблон для поиска всех исходящих sc-коннекторов из указанного sc-элемента будет тройкой:

* где первый sc-элемент известен как `f`;
* второй и третий sc-элементы должны быть найдены как `a`.

Существует три возможных типа тройных sc-шаблонов:

* `f_a_a` — sc-шаблон для поиска всех исходящих sc-коннекторов из указанного sc-элемента;
* `f_a_f` — sc-шаблон для поиска всех sc-коннекторов между двумя указанными sc-элементами;
* `a_a_f` — sc-шаблон для поиска всех входящих sc-коннекторов в указанный sc-элемент.

Существует семь возможных типов пятиэлементных sc-шаблонов:

* `f_a_a_a_a` — sc-шаблон для поиска всех исходящих sc-коннекторов из указанного sc-элемента со всеми атрибутами этих sc-коннекторов;
* `f_a_f_a_a` — sc-шаблон для поиска всех sc-коннекторов между двумя указанными sc-элементами со всеми атрибутами этих sc-коннекторов;
* `f_a_a_a_f` — sc-шаблон для поиска всех исходящих sc-коннекторов из указанного sc-элемента с указанным атрибутом;
* `f_a_f_a_f` — sc-шаблон для поиска всех sc-коннекторов между двумя указанными sc-элементами с указанным атрибутом;
* `a_a_f_a_a` — sc-шаблон для поиска всех входящих sc-коннекторов в указанный sc-элемент со всеми атрибутами этих sc-коннекторов;
* `a_a_f_a_f` — sc-шаблон для поиска всех входящих sc-коннекторов в указанный sc-элемент с указанным атрибутом;
* `a_a_a_a_f` — sc-шаблон для поиска всех sc-коннекторов с указанным атрибутом.

Здесь атрибут — это sc-элемент, от которого sc-коннектор исходит к искомым sc-коннекторам.

Существуют некоторые методы, доступные для класса `ScTemplate`:

### **Triple**

Это метод, который добавляет тройную sc-конструкцию в sc-шаблон. Есть несколько примеров использования этой функции для создания простых sc-шаблонов:

<table>
  <tr>
    <th>Шаблон</th>
    <th>Описание</th>
  </tr>

  <tr>
    <td>f_a_a</td>
    <td>
      <strong>Графическое представление</strong>
      <br/><scg src="../images/templates/template_triple_f_a_a_example.gwf"></scg>
      <br/><strong>Соответствующий код на C++</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Triple(
  param1,
  ScType::VarPermPosArc,
  ScType::VarNode
);
</code></pre>
      <br/>Это трехэлементный sc-шаблон используется для обхода исходящих sc-коннекторов из указанного sc-элемента.
      <br/> <code>param1</code> - это известный sc-адрес sc-элемента. Он должен быть действительным (используйте метод <code>IsElement</code> для проверки). Параметры <code>_param2</code> и <code>_param3</code> представляют собой sc-типы, которые будут использоваться для сравнения в поисковом движке. Когда поисковый движок будет обходить исходящие sc-коннекторы из <code>param1</code>, в результат обхода будет добавлена конструкция, где исходящие sc-дуги из <code>param1</code> будет соответствовать указанному типу <code>_param2</code>, а тип конечного sc-элемента этой sc-дуги будет соответствовать типу <code>_param3</code>.
    <br/>Вы можете использовать любой sc-тип <code>_param3</code> (включая sc-коннекторы) в зависимости от sc-конструкции, которую вы хотите найти. Но <code>_param2</code> должен быть любым sc-типом переменного sc-коннектора.
    </td>
  </tr>



  <tr>
    <td>f_a_f</td>
    <td>
      <strong>Графическое представление</strong>
      <br/><scg src="../images/templates/template_triple_f_a_f_example.gwf"></scg>
      <br/><strong>Соответствующий код на C++</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Triple(
  param1,
  ScType::VarPermPosArc,
  param3
);
</code></pre>
      <br/> Этот тройной sc-шаблон используется для поиска sc-дуги между <code>param1</code> и <code>param3</code>.
      <br/>Существуют <code>param1</code> и <code>param3</code>, известные <code>ScAddr</code> sc-элементов. Тип Arc <code>_param2</code> должен быть переменным.
    </td>
  </tr>
  <tr>
    <td>a_a_f</td>
    <td>
      <strong>Графическое представление</strong>
      <br/><scg src="../images/templates/template_triple_a_a_f_example.gwf"></scg>
      <br/><strong>Соответствующий код на C++</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Triple(
  ScType::VarNode,
  ScType::VarPermPosArc,
  param3
);
</code></pre>
      <br/>Этот трехэлементный sc-шаблон используется для перемещения входящих sc-коннекторов к указанному sc-элементу.
      <br/>Здесь <code>param3</code> - это известный sc-адрес sc-элемента. Вы можете использовать любой тип <code>_param1</code> (включая sc-коннекторы) в зависимости от конструкции, которую вы хотите найти. Но <code>_param2</code> должен быть переменным коннектором любого типа.
    </td>
  </tr>
</table>

### **Quintuple**

Это метод, который добавляет пятиэлементную sc-конструкцию в sc-шаблон. Есть несколько примеров использования этой
функции для создания простых sc-шаблонов:

<table>
  <tr>
    <th>Шаблон</th>
    <th>Описание</th>
  </tr>

  <tr>
    <td>f_a_a_a_a</td>
    <td>
      <strong>Графическое представление</strong>
      <br/><scg src="../images/templates/template_quintuple_f_a_a_a_a_example.gwf"></scg>
      <br/><strong>Соответствующий код на C++</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Quintuple(
  param1,
  ScType::VarPermPosArc,
  ScType::VarNode,
  ScType::VarPermPosArc,
  ScType::VarNode
);
</code></pre>
    </td>
  </tr>

  <tr>
    <td>f_a_f_a_a</td>
    <td>
      <strong>Графическое представление</strong>
      <br/><scg src="../images/templates/template_quintuple_f_a_f_a_a_example.gwf"></scg>
      <br/><strong>Соответствующий код на C++</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Quintuple(
  param1,
  ScType::VarPermPosArc,
  param3,
  ScType::VarPermPosArc,
  ScType::VarNode
);
</code></pre>
    </td>
  </tr>

  <tr>
    <td>f_a_a_a_f</td>
    <td>
      <strong>Графическое представление</strong>
      <br/><scg src="../images/templates/template_quintuple_f_a_a_a_f_example.gwf"></scg>
      <br/><strong>Соответствующий код на C++</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Quintuple(
  param1,
  ScType::VarPermPosArc,
  ScType::VarNode,
  ScType::VarPermPosArc,
  param5
);
</code></pre>
    </td>
  </tr>

  <tr>
    <td>f_a_f_a_f</td>
    <td>
      <strong>Графическое представление</strong>
      <br/><scg src="../images/templates/template_quintuple_f_a_f_a_f_example.gwf"></scg>
      <br/><strong>Соответствующий код на C++</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Quintuple(
  param1,
  ScType::VarPermPosArc,
  param3,
  ScType::VarPermPosArc,
  param5
);
</code></pre>
    </td>
  </tr>

  <tr>
    <td>a_a_f_a_a</td>
    <td>
      <strong>Графическое представление</strong>
      <br/><scg src="../images/templates/template_quintuple_a_a_f_a_a_example.gwf"></scg>
      <br/><strong>Соответствующий код на C++</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Quintuple(
  ScType::VarNode,
  ScType::VarPermPosArc,
  param3,
  ScType::VarPermPosArc,
  ScType::VarNode
);
</code></pre>
    </td>
  </tr>

  <tr>
    <td>a_a_f_a_f</td>
    <td>
      <strong>Графическое представление</strong>
      <br/><scg src="../images/templates/template_quintuple_a_a_f_a_f_example.gwf"></scg>
      <br/><strong>Соответствующий код на C++</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Quintuple(
  ScType::VarNode,
  ScType::VarPermPosArc,
  param3,
  ScType::VarPermPosArc,
  param5
);
</code></pre>
    </td>
  </tr>

  <tr>
    <td>a_a_a_a_f</td>
    <td>
      <strong>Графическое представление</strong>
      <br/><scg src="../images/templates/template_quintuple_a_a_a_a_f_example.gwf"></scg>
      <br/><strong>Соответствующий код на C++</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Quintuple(
  ScType::VarNode,
  ScType::VarPermPosArc,
  ScType::VarNode,
  ScType::VarPermPosArc,
  param5
);
</code></pre>
    </td>
  </tr>
</table>

Когда поисковая система sc-template работает, она пытается просмотреть граф по простому (трехэлементному или пятиэлементному) sc-шаблону в указанном порядке. 
Например, нам нужно проверить, включен ли указанный sc-элемент (`_set`) в классы `concept_set` и `concept_binary_relation`:

<scg src="../images/templates/template_example_2.gwf"></scg>

Вот пример кода таких шаблонов.

```cpp
...
// Найдите ключевые понятия, которые следует использовать в sc-шаблоне.
ScAddr const & conceptSetAddr = context.SearchElementBySystemIdentifier("concept_set");
ScAddr const & conceptBinaryRelationAddr 
    = context.SearchElementBySystemIdentifier("concept_binary_relation");

// Сгенерируйте sc-шаблон и добавьте тройку элементов в этот sc-шаблон.
ScTemplate templ;
templ.Triple(
  conceptSetAddr,    // sc -адрес узла concept set
  ScType::VarPermPosArc,
  ScType::VarNode >> "_set"
);
templ.Triple(
  conceptBinaryRelationAddr,    // sc -адрес узла concept binary relation
  ScType::VarPermPosArc,
  "_set"
);
..
```

В коде вы можете увидеть конструкцию "ScType::VarNode >> "_set"" - это имя для sc-элемента sc-шаблона.
Это позволяет задать псевдоним для указанного sc-элемента в sc-шаблоне и использовать его много раз в разных тройках. Вы можете видеть,
что во второй тройке мы используем этот псевдоним ""_set"`. Это означает, что нам нужно поместить результат поиска из
первой тройки во вторую. Итак, вторая тройка - это тройка в стиле `f_a_f`.


Итак, если вы хотите использовать один и тот же sc-элемент "_x" в разных тройках, и вы не знаете, что это за "ScAddr", тогда просто используйте два
основных правила:

* Задайте псевдоним этому sc-элементу при первом появлении этого sc-элемента в тройках sc-шаблона. Для этого вам нужно использовать оператор `>>`
(смотрите код ниже, последний sc-элемент первой тройки).
* Если вам нужно использовать псевдоним sc-элемента в следующих тройках, просто используйте его псевдоним вместо `ScType` или `ScAddr` (смотрите код
ниже, первый sc-элемент второй тройки).

Вот пример кода с именованием(псевдонимом).

```cpp
...
ScTemplate templ;
templ.Triple(
  anyAddr, // sc -адрес известного sc-элемента
  ScType::VarPermPosArc,  // тип неизвестной sc-дуги
  ScType::VarNode >> "_x"  // тип и псевдоним для неизвестного sc-элемента
);
templ.Triple(
  "_x",  // скажем, это тот же sc-элемент, что и последний в предыдущей тройке
  ScType::VarPermPosArc,  // тип неизвестной sc-дуги
  ScType::VarNode  // тип неизвестного sc-узла
);
...
```

Внутри программного объекта sc-шаблона все его конструкции представлены в виде троек.

### **HasReplacement**

Чтобы проверить, что sc-шаблон содержит псевдоним sc-элемента, вы можете использовать метод `HasReplacement`.

```cpp
...
ScTemplate templ;
templ.Triple(
  anyAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);
bool const hasAliasX = templ.HasReplacement("_x");
// Значение `hasAliasX` должно быть равно `true`.
...
```

### **Size**

Чтобы получить количество троек в sc-шаблоне, используйте метод "Size". Это может быть полезно, если ваша программа может выбрать оптимальный для
работы sc-шаблон.

```cpp
ScTemplate templ;
templ.Triple(
  anyAddr, // sc-address of known sc-element
  ScType::VarPermPosArc,  // type of unknown sc-arc
  ScType::VarNode >> "_x"  // type and alias for an unknown sc-element
);
templ.Triple(
  "_x",  // say that is the same sc-element as the last on in a previous triple
  ScType::VarPermPosArc,  // type of unknown sc-arc
  ScType::VarNode  // type of unknown sc-node
);

size_t const tripleCount = templ.Size();
// Количество троек должно быть рано `2`.
...
```

### **IsEmpty**

Если вам нужно, чтобы sc-шаблон был пустым, вам не нужно добавлять в него какие-либо конструкции. Но вы должны знать
, что результат генерации по этому sc-шаблону всегда "true", а результат поиска по этому sc-шаблону всегда
`false`. Чтобы проверить, что sc-шаблон пуст, используйте метод `isEmpty`.

```cpp
...
ScTemplate templ;
bool const isEmpty = templ.IsEmpty();
// Значение `isEmpty` должно быть равно `true`.
...
```

## **ScTemplateBuild**

Кроме того, вы можете создавать sc-шаблоны, используя [SCs-code](../../../../scs/scs.md).

```cpp
...
// Опишите свой sc-шаблон в SCs-коде.
sc_char const * data = 
  "_set"
  "  _<- concept_set;"
  "  _<- concept_binary_set;;";

// Создайте программный объект по этому sc-шаблону.
ScTemplate templ;
context.BuildTemplate(templ, data);
...
```

!!! note
    Не используйте результирующее значение, оно ничего не значит.

При построении sc-шаблона все константы будут определены по их системному идентификатору (
например, `concept_set`, `concept_binary_set`), поэтому в результате `templ` будет содержать sc-шаблон:

<scg src="../images/templates/template_example_2.gwf"></scg>

Или вы можете сделать это, указав действительный sc-адрес некоторого sc-шаблона в sc-памяти.

```cpp
...
// Найдите по системному идентификатору ваш sc-шаблон в sc-памяти.
ScAddr const & templAddr = context.SearchElementBySystemIdentifier("my_template");

// Создайте программный объект по этому sc-шаблону.
ScTemplate templ;
context.BuildTemplate(templ, templAddr);
...
```

!!! note
    Не используйте результирующее значение, оно ничего не значит.

## **ScTemplateParams**

You can replace existing sc-variables in sc-templates by your ones. To provide different replacements for sc-variables 
in different cases there is class `ScTemplateParams`. It stores a map between sc-variables and specified values (replacements).

```cpp
...
ScTemplateParams params;
...
```

### **Add**

You can add replacement for sc-variable by specifying system identifier of this sc-variable if sc-template is built from
SCs-code.

```cpp
...
// Describe your sc-template on SCs-code.
sc_char const * data = 
  "_set"
  "  _<- concept_set;"
  "  _<- concept_binary_set;;";

// Generate replacement in sc-memory.
ScAddr const & setAddr = context.GenerateNode(ScType::ConstNode);
// Also you can find some replacement from sc-memory.

// Define replacements for sc-variables in sc-template.
ScTemplateParams params;
params.Add("_set", setAddr);

// Build program object by this sc-template, specifying replacements.
ScTemplate templ;
context.BuildTemplate(templ, data, params);
...
```

Or you can add replacement for sc-variable by specifying sc-address of this sc-variable if sc-template is built from 
sc-address of sc-structure in sc-memory.

```cpp
...
// Find by system identifier your sc-template in sc-memory.
ScAddr const & templAddr = context.SearchElementBySystemIdentifier("my_template");

// Find by system identifier sc-address of sc-variable in your sc-template.
ScAddr const & setVarAddr = context.SearchElementBySystemIdentifier("_set");

// Generate replacement in sc-memory.
ScAddr const & setAddr = context.GenerateNode(ScType::ConstNode);
// Also you can find some replacement from sc-memory.

// Define replacements for sc-variables in sc-template.
ScTemplateParams params;
params.Add(setVarAddr, setAddr);

// Build program object by this sc-template, specifying replacements.
ScTemplate templ;
context.BuildTemplate(templ, templAddr, params);
...
```

### **Get**

Sometimes you need to check if there is replacement in params yet. You can do it using the method `Get`. It works with
system identifiers and sc-addresses of sc-variables also.

```cpp
...
// Generate replacement in sc-memory.
ScAddr const & setAddr = context.GenerateNode(ScType::ConstNode);
// Also you can find some replacement from sc-memory.

// Define replacements for sc-variables in sc-template.
ScTemplateParams params;
params.Add("_set", setAddr);

ScAddr const & replAddr = params.Get("_set");
// The value of `replAddr` be equal to value of `setAddr`.
...
```

```cpp
...
// Find by system identifier sc-address of sc-variable in your sc-template.
ScAddr const & setVarAddr = context.SearchElementBySystemIdentifier("_set");

// Generate replacement in sc-memory.
ScAddr const & setAddr = context.GenerateNode(ScType::ConstNode);
// Also you can find some replacement from sc-memory.

// Define replacements for sc-variables in sc-template.
ScTemplateParams params;
params.Add(setVarAddr, setAddr);

ScAddr const & replAddr = params.Get(setVarAddr);
// The value of `replAddr` be equal to value of `setAddr`.
...
```

!!! note
    If there are no replacements by specified system identifier or sc-address of sc-variable of sc-template then the 
    method `Get` will return empty sc-address.

### **IsEmpty**

To check that replacements map is empty use the method `IsEmpty`.

```cpp
...
ScTemplateParams params;
bool const isEmpty = params.IsEmpty();
// The value of `isEmpty` be equal to `true`.
...
```

## **GenerateByTemplate**

Use sc-template to generate graphs in sc-memory and get replacements from result.

```cpp
...
// Specify sc-template for searching sc-constructions in sc-memory.
// You can use `ScTemplate` methods or method ScTemplateBuild to translate 
// sc-template from SCs-code or sc-memory into program representation.
ScTemplateResultItem result;
context.GenerateByTemplate(templ, result);
// Sc-elements sc-addresses of generated sc-construction may be gotten from 
// `result`.
...
```

!!! note 
    Remember, that sc-template must contain only valid sc-address of sc-elements and all sc-connectors in it must be
    sc-variables. Otherwise, this method can throw `utils::ExceptionInvalidParams` with description of this error.

## **ScTemplateResultItem**

It is a class that stores information about sc-construction.

### **Safe Get**

You can get sc-addresses of sc-elements of generated sc-construction by system identifier or sc-address of sc-variable 
in sc-template. To do it safely (without throwing exceptions if there are no replacements by specified system identifier 
or sc-address of sc-variable of sc-template) use the methods `Get` and provide result of replacement as out parameter in
this method.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);

ScTemplateResultItem result;
context.GenerateByTemplate(templ, result);

ScAddr setAddr;
bool replExist = result.Get("_x", setAddr);
// The value of `replExist` be equal to `true`.

bool replExist = result.Get("_y", setAddr);
// The value of `replExist` be equal to `false`.
...
```

### **Get**

If you want to catch exceptions, if there are no replacements by specified system identifier or sc-address of sc-variable 
of sc-template, use the method `Get` and get replacement as result of this method. Then this method will throw 
`utils::ExceptionInvalidParams` with description of error.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);

ScTemplateResultItem result;
context.GenerateByTemplate(templ, result);

ScAddr setAddr = result.Get("_x");

setAddr = result.Get("_y", setAddr);
// It will throw the exception `utils::ExceptionInvalidParams`.
...
```

### **Has**

To check that there is replacement by specified system identifier or sc-address of sc-variable of sc-template use the
method `Has`.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);

ScTemplateResultItem result;
context.GenerateByTemplate(templ, result);

bool const replExist = result.Has("_x");
// The value of `replExist` be equal to `true`.
...
```

### **operator[]**

To get all replacements in result you can use the `operator[]`. It returns replacement by index of sc-variable in 
sc-template. If there is no sc-variable with specified index this method will throw the exception 
`utils::ExceptionInvalidParams` with description of the error.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);

ScTemplateResultItem result;
context.GenerateByTemplate(templ, result);

ScAddr const & setAddr = result[2];
// It is equal to `result.Get("_x")`.
...
```

### **Size**

If you want to iterate all replacement in the result you need to know size of this result.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);

ScTemplateResultItem result;
context.GenerateByTemplate(templ, result);

// Iterate by all replacements in result.
for (size_t i = 0; i < result.Size(); ++i)
{
  ScAddr const & addr = result[i];
  // Implement your code to handle replacements.
}
...
```

!!! note
    The method `Size` returns summary count of indexes of replacements in each triple in sc-template. If there are 
    `2` triples in sc-template, then there are `2 * 3 = 6` different indexes of replacements in sc-template.

## **SearchByTemplate**

You can search sc-construction in sc-memory by sc-templates. This search refers to isomorphic search by graph-template.
Search algorithm trying to find all possible variants of specified construction. It uses any constants (available
sc-addresses from parameters to find equal sc-constructions in sc-memory).

```cpp
...
// Specify sc-template for searching sc-constructions in sc-memory.
// You can use `ScTemplate` methods or method ScTemplateBuild to translate 
// sc-template from SCs-code or sc-memory into program representation.
ScTemplateSearchResult result;
bool const isFoundByTemplate = context.SearchByTemplate(templ, result);
// Program representation of sc-constructions in `ScTemplateResultItem` 
// may be gotten from `result`.
...
```

!!! note 
    Remember, that sc-template must contain only valid sc-address of sc-elements and all sc-connectors in it must be
    sc-variables. Otherwise, this method can throw `utils::ExceptionInvalidParams` with description of this error.

## **ScTemplateSearchResult**

It is a class that stores in information about sc-constructions represented in `ScTemplateResultItem`.
An object of class `ScTemplateSearchResult` can be referred to a vector of objects of class `ScTemplateResultItem`.

### **Safe Get**

To get object of class `ScTemplateResultItem` you can use the method `Get`. If you want to get objects safely, use the
method `Get` and provide `ScTemplateResultItem` as out parameter in this method.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);
// There is one sc-construction that is isomorphic this sc-template.

ScTemplateSearchResult result;
bool const isFoundByTemplate = context.SearchByTemplate(templ, result);

ScTemplateResultItem item;
bool constrExist = result.Get(0, item);
// The value of `constrExist` be equal to `true`.

constrExist = result.Get(1, item);
// The value of `constrExist` be equal to `false` and item is not valid.
...
```

### **Get**

If you want to catch exceptions use the method `Get` and get result as return value. If there is no sc-construction with 
specified index this method will throw the exception utils::ExceptionInvalidParams with description of the error.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);
// There is one sc-construction that is isomorphic this sc-template.

ScTemplateSearchResult result;
bool const isFoundByTemplate = context.SearchByTemplate(templ, result);

ScTemplateResultItem item = result.Get(0);
// It is a valid item.

item = result.Get(1);
// It throws `utils::ExceptionInvalidParams`.
...
```

### **operator[]**

Also, you can use the method `operator[]` to do this. If there is no sc-construction with specified index this method 
will throw the exception utils::ExceptionInvalidParams with description of the error.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);
// There is one sc-construction that is isomorphic this sc-template.

ScTemplateSearchResult result;
bool const isFoundByTemplate = context.SearchByTemplate(templ, result);

ScTemplateResultItem item = result[0];
// It is a valid item.

item = result[1];
// It throws `utils::ExceptionInvalidParams`.
...
```

### **Size**

To get count of found sc-constructions by sc-template you can use the method `Size`.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);
// There is one sc-construction that is isomorphic this sc-template.

ScTemplateSearchResult result;
bool const isFoundByTemplate = context.SearchByTemplate(templ, result);

size_t const count = result.Size();
// The value of `count` be equal to `1`.
...
```

### **IsEmpty**

To check if found result is empty use the method `IsEmpty`.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);
// There is one sc-construction that is isomorphic this sc-template.

ScTemplateSearchResult result;
bool const isFoundByTemplate = context.SearchByTemplate(templ, result);

bool const count = result.IsEmpty();
// The value of `count` be equal to `false`.
...
```

### **Clear**

To clear all information about found sc-constructions use the method `Clear`.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);
// There is one sc-construction that is isomorphic this sc-template.

ScTemplateSearchResult result;
bool const isFoundByTemplate = context.SearchByTemplate(templ, result);

result.Clear();
// After that `result` does not contain any information about sc-constructions.
...
```

### **ForEach**

To iterate all program objects of found sc-constructions by sc-template you can use for-each cycle.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);
// There is one sc-construction that is isomorphic this sc-template.

ScTemplateSearchResult result;
bool const isFoundByTemplate = context.SearchByTemplate(templ, result);

for (size_t i = 0; i < result.Size(); ++i)
{
  ScTemplateResultItem const & item = result.Get(i);
  // Implement logic to handle found sc-constructions.
}
...
```

Or you can use the method `ForEach` to do this.

```cpp
...
ScTemplate templ;
templ.Triple(
  conceptSetAddr,
  ScType::VarPermPosArc,
  ScType::VarNode >> "_x"
);
// There is one sc-construction that is isomorphic this sc-template.

ScTemplateSearchResult result;
bool const isFoundByTemplate = context.SearchByTemplate(templ, result);

result.ForEach([](ScTemplateResultItem const & item) {
  // Implement logic to handle found sc-constructions.
});
...
```

## **SearchByTemplateInterruptibly**

This method searches constructions by isomorphic sc-template and pass found sc-constructions to `callback` 
lambda-function. Lambda-function `callback` must return a request command value to manage sc-template search:

- ScTemplateSearchRequest::CONTINUE,
- ScTemplateSearchRequest::STOP,
- ScTemplateSearchRequest::ERROR.

When ScTemplateSearchRequest::CONTINUE returns, sc-template search will be continued. If ScTemplateSearchRequest::STOP 
or ScTemplateSearchRequest::ERROR returns, then sc-template search stops. If sc-template search stopped by 
ScTemplateSearchRequest::ERROR, then SearchByTemplateInterruptibly thrown utils::ExceptionInvalidState. If `filterCallback` 
passed, then all found sc-constructions triples are filtered by `filterCallback` condition.

```cpp
...
ScAddr const & structureAddr = context.SearchElementBySystemIdentifier("my_structure");
ScAddr const & setAddr = context.SearchElementBySystemIdentifier("my_set");
ScAddr const & classAddr = context.SearchElementBySystemIdentifier("my_class");

ScTemplate templ;
templ.Triple(
  classAddr,
  ScType::VarPermPosArc >> "_arc",
  ScType::Unknown >> "_addr2"
);
m_context->SearchByTemplateInterruptibly(templ, [&context](
    ScTemplateSearchResultItem const & item) -> ScTemplateSearchRequest 
{
  ScAddr const & arcAddr = item["_arc"];
  if (context->CheckConnector(
      structureAddr, arcAddr, ScType::ConstPermPosArc))   
    return ScTemplateSearchRequest::CONTINUE;

  if (context.GenerateConnector(
      ScType::ConstTempPosArc, setAddr, item["_addr2"]))
    return ScTemplateSearchRequest::STOP;

  return ScTemplateSearchRequest::ERROR;
});
...
```

--- 

## **Frequently Asked Questions**

- [What's the best way to describe sc-templates for search? By sc-template C++ API or on the SC-code?](#what-is-the-best-way-to-describe-sc-templates-for-search-by-sc-template-c-api-or-on-the-sc-code)
- [Which is better: searching by sc-template or by iterator?](#which-is-better-searching-by-sc-template-or-by-iterator)

### **What is the best way to describe sc-templates for search? By sc-template C++ API or on the SC-code?**

The description of sc-templates in the knowledge base encourages the use of reflection for them. The sc-templates 
described in the knowledge base can be easily expanded and improved. However, sc-templates presented through the API 
do not require preprocessing (translation from the knowledge base), so the speed of the program used by such 
sc-templates may be higher if the sc-templates have significant size.

### **Which is better: searching by sc-template or by iterator?**

In all cases sc-iterator is faster the searching by sc-template. If you want to search large sc-constructions, then don't
search they by one large sc-template, divide it into certain sc-templates or use sc-iterators instead of sc-template.
