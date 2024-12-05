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
    <th>Template</th>
    <th>Description</th>
  </tr>

  <tr>
    <td>f_a_a</td>
    <td>
      <strong>Graphical representation</strong>
      <br/><scg src="../images/templates/template_triple_f_a_a_example.gwf"></scg>
      <br/><strong>Equal C++ code</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Triple(
  param1,
  ScType::VarPermPosArc,
  ScType::VarNode
);
</code></pre>
      <br/>This triple sc-template is used to traverse outgoing sc-connectors from specified sc-element.
      <br/>There <code>param1</code> is a known sc-address of sc-element. It must be a valid (use <code>IsElement</code> method to check). Where <code>_param2</code> and <code>_param3</code> are sc-types for compare by search engine. When search engine will traverse outgoing sc-connectors from <code>param1</code>. Construction will be added into traverse result, where outgoing sc-arc from <code>param1</code>, will suitable to specified type <code>_param2</code>, and type of target sc-element of this sc-arc will be suitable for a type <code>_param3</code>.
      <br/>You can use any sc-type of <code>_param3</code> (including sc-connectors) depending on sc-construction you want to find. But <code>_param2</code> should be any sc-type of variable sc-connector.
    </td>
  </tr>

  <tr>
    <td>f_a_f</td>
    <td>
      <strong>Graphical representation</strong>
      <br/><scg src="../images/templates/template_triple_f_a_f_example.gwf"></scg>
      <br/><strong>Equal C++ code</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Triple(
  param1,
  ScType::VarPermPosArc,
  param3
);
</code></pre>
      <br/>This triple sc-template using to find sc-arc between <code>param1</code> and <code>param3</code>.
      <br/>There are <code>param1</code> and <code>param3</code> a known <code>ScAddr</code> of sc-elements. Arc type <code>_param2</code> should be variable.
    </td>
  </tr>
  <tr>
    <td>a_a_f</td>
    <td>
      <strong>Graphical representation</strong>
      <br/><scg src="../images/templates/template_triple_a_a_f_example.gwf"></scg>
      <br/><strong>Equal C++ code</strong>
      <br/>
<pre><code class="cpp">
ScTemplate templ;
templ.Triple(
  ScType::VarNode,
  ScType::VarPermPosArc,
  param3
);
</code></pre>
      <br/>This triple sc-template using to traverse incoming sc-connectors to specified sc-element.
      <br/>There <code>param3</code> is a known sc-address of sc-element. You can use any type of <code>_param1</code> (including sc-connectors) depending on construction you want to find. But <code>_param2</code> should be any type of variable connector.
    </td>
  </tr>
</table>

### **Quintuple**

It is the method that adds quintuple sc-construction into sc-template. There are some examples of using this 
function to produce simple sc-templates:

<table>
  <tr>
    <th>Template</th>
    <th>Description</th>
  </tr>

  <tr>
    <td>f_a_a_a_a</td>
    <td>
      <strong>Graphical representation</strong>
      <br/><scg src="../images/templates/template_quintuple_f_a_a_a_a_example.gwf"></scg>
      <br/><strong>Equal C++ code</strong>
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
      <strong>Graphical representation</strong>
      <br/><scg src="../images/templates/template_quintuple_f_a_f_a_a_example.gwf"></scg>
      <br/><strong>Equal C++ code</strong>
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
      <strong>Graphical representation</strong>
      <br/><scg src="../images/templates/template_quintuple_f_a_a_a_f_example.gwf"></scg>
      <br/><strong>Equal C++ code</strong>
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
      <strong>Graphical representation</strong>
      <br/><scg src="../images/templates/template_quintuple_f_a_f_a_f_example.gwf"></scg>
      <br/><strong>Equal C++ code</strong>
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
      <strong>Graphical representation</strong>
      <br/><scg src="../images/templates/template_quintuple_a_a_f_a_a_example.gwf"></scg>
      <br/><strong>Equal C++ code</strong>
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
      <strong>Graphical representation</strong>
      <br/><scg src="../images/templates/template_quintuple_a_a_f_a_f_example.gwf"></scg>
      <br/><strong>Equal C++ code</strong>
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
      <strong>Graphical representation</strong>
      <br/><scg src="../images/templates/template_quintuple_a_a_a_a_f_example.gwf"></scg>
      <br/><strong>Equal C++ code</strong>
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
