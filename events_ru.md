# **ScEvent API**

!!! примечание
    Это верно только для версий sc-machine, которые >= 0.10.0.
--- 

Этот API описывает, как работать с sc-событиями.

!!! примечание
    Чтобы подключить этот API, укажите "#include <sc-memory/sc_event.hpp>` в вашем исходном файле hpp.

## **Что такое событийно-ориентированная модель?**

Sc-машина использует событийно-управляемую модель для управления обработкой sc-конструкций. В sc-памяти хранятся конструкции SC-кода, которые представляют собой графовые структуры, тогда любые события, происходящие в sc-памяти, связаны с изменениями в этих графовых конструкциях.

Методы, которые генерируют события:

- `GenerateConnector`, 
- `EraseElement`,
- `SetLinkContent`.

Они публикуют события в очереди событий, не зная, какие пользователи их получат. Эти компоненты фильтруют и распределяют события среди соответствующих пользователей. Они управляют потоком событий и гарантируют, что они дойдут до нужных адресатов. Потребители событий - это компоненты, которые отслеживают и обрабатывают события. Потребителями событий могут быть модули, агенты или что-то еще. Смотрите [**FAQ**](#frequently-asked-questions), чтобы узнать, почему это работает именно так.

В рамках технологии OSTIS событиями считаются только ситуации, в которых изменились взаимосвязи или были созданы новые взаимосвязи, или содержание ссылок было изменено, или какой-либо sc-элемент был удален.

## **ScEvent**

sc-машина предоставляет функционал для подписки следующих элементарных типов sc-событий:

* `ScElementaryEvent` является базовым классом для всех sc-событий, его можно использовать для обработки всех sc-событий для указанного sc-элемента;
* `ScEventAfterGenerateConnector`, генерируется каждый раз, когда генерируется sc-коннектор от указанного sc-элемента или к нему;
* `ScEventAfterGenerateOutgoingArc`, генерируется каждый раз, когда генерируется исходящая sc-дуга от указанного sc-элемента;
* `ScEventAfterGenerateIncomingArc`, генерируется каждый раз, когда генерируется входящая sc-дуга для указанного sc-элемента;
* `ScEventAfterGenerateEdge`, генерируется каждый раз, когда создается sc-ребро от или к указанному sc-элементу;
* `ScEventBeforeEraseConnector`, генерируется каждый раз, когда sc-коннектор от указанного sc-элемента или к нему стирается;
* `ScEventBeforeEraseOutgoingArc`, генерируется каждый раз, когда удаляется исходящая sc-дуга из указанного sc-элемента;
* `ScEventBeforeEraseIncomingArc`, генерируется каждый раз, когда входящая sc-дуга к указанному sc-элементу удаляется;
* `ScEventBeforeEraseEdge`, генерируется каждый раз, когда удаляется sc-ребро от указанного sc-элемента или к нему;
* `ScEventBeforeEraseElement`, испускается, когда указанный sc-элемент удаляется;
* `ScEventBeforeChangeLinkContent`, срабатывает каждый раз, когда изменяется содержимое указанной sc-ссылки.

Им соответствуют следующие классы:

* `class ScElementaryEvent`;
* `template <ScType const & connectorType> class ScEventAfterGenerateConnector`;
* `template <ScType const & arcType> class ScEventAfterGenerateOutgoingArc`;
* `template <ScType const & arcType> class ScEventAfterGenerateIncomingArc`;
* `template <ScType const & edgeType> class ScEventAfterGenerateEdge`;
* `template <ScType const & connectorType> class ScEventBeforeEraseConnector`;
* `template <ScType const & arcType> class ScEventBeforeEraseOutgoingArc`;
* `template <ScType const & arcType> class ScEventBeforeEraseIncomingArc`;
* `template <ScType const & edgeType> class ScEventBeforeEraseEdge`;
* `class ScEventBeforeEraseElement`;
* `class ScEventBeforeChangeLinkContent`.

!!! примечание
    Все эти классы sc-событий унаследованы от класса `ScElementaryEvent`. Класс `ScElementaryEvent` унаследован от класса `ScEvent`, который является абстрактным классом.
    
!!! примечание
    Типы sc-коннекторов указаны для sc-событий добавления (удаления) sc-дуги (sc-ребра).
    
!!! предупреждение
    Вы не можете создавать объекты этих классов. Все конструкторы этих классов являются закрытыми(private).

!!! предупреждение
    Все объекты классов sc-event не подлежат копированию и перемещению.

### **ScElementaryEvent**

Этот класс предоставляет три общих метода для всех sc-событий: `GetUser`, `GetSubscriptionElement` и `GetTriple`.

#### **GetUser**

Используйте этот метод, чтобы получить sc-адрес того, кто инициировал текущее sc-событие.

```cpp
...
ScAddr const userAddr = event.GetUser();
...
```

#### **GetSubscriptionElement**

Каждое событие происходит в семантической окрестности некоторого sc-элемента. Этот sc-элемент называется sc-элементом подписки на sc-событие. Другими словами, это элемент, который постоянно отслеживает возникновение определенных sc-событий в пределах своей окрестности. Чтобы получить его, воспользуйтесь этим методом.

```cpp
...
ScAddr const subscriptionElement = event.GetSubscriptionElement();
...
```

#### **GetTriple**

Чтобы получить информацию о sc-элементах в инициированном sc-событии, вы можете использовать этот метод. Он возвращает кортеж из трех ScAddr. Для получения дополнительной информации смотрите таблицу ниже.

<table width="95%">
  <tr>
    <th>Класс</th>
    <th>Описание</th>
  </tr>

  <tr>
  <td><strong>ScEventAfterGenerateConnector</strong></td>
  <td>
      <scg src="../images/events/sc_event_connector.gwf"></scg>
      <strong>Пример кода на C++</strong>:
      <pre><code class="cpp">
...
auto const [subscriptionElementAddr, connectorAddr, otherAddr] 
  = event.GetTriple();
// `subscriptionElementAddr` — это sc-адрес начального или
// конечного sc-элемента (прослушивать на нем sc-событие).
// `connectorAddr` — это sc-адрес сгенерированного sc-коннектора
// к или от `subscriptionElementAddr`.
// `otherAddr` — это sc-адрес начального или конечного sc-элемента
// `arcAddr`.
...
      </code></pre>
    </td>
  </tr>

  <tr>
    <td><strong>ScEventAfterGenerateOutgoingArc</strong></td>
    <td>
      <scg src="../images/events/sc_event_outgoing_arc.gwf"></scg>
      <strong>Пример кода на C++</strong>:
      <pre><code class="cpp">
...
auto const [subscriptionElementAddr, arcAddr, otherAddr] 
  = event.GetTriple();
// `subscriptionElementAddr` - это sc-адрес начального sc-элемента 
// (прослушивать его в sc-событии).
// `arcAddr` - это sc-адрес сгенерированной исходящей sc-дуги 
// из `subscriptionElementAddr`.
// `otherAddr` - это sc-адрес конечного sc-элемента `arcAddr`.
...
      </code></pre>
    </td>
  </tr>

  <tr>
    <td><strong>ScEventAfterGenerateIncomingArc</strong></td>
    <td>
      <scg src="../images/events/sc_event_incoming_arc.gwf"></scg>
      <strong>Пример кода на C++</strong>:
      <pre><code class="cpp">
...
auto const [subscriptionElementAddr, arcAddr, otherAddr] 
  = event.GetTriple();
// `subscriptionElementAddr` - это sc-адрес конечного sc-элемента 
// (прослушивать его в sc-событии).
// `arcAddr` - это sc-адрес сгенерированной входящей sc-дуги 
// для `subscriptionElementAddr`.
// `otherAddr` - это sc-адрес начального sc-элемента `arcAddr`.
...
      </code></pre>
    </td>
  </tr>

  <tr>
    <td><strong>ScEventAfterGenerateEdge</strong></td>
    <td>
      <scg src="../images/events/sc_event_edge.gwf"></scg>
      <strong>Пример кода на C++</strong>:
      <pre><code class="cpp">
...
auto const [subscriptionElementAddr, edgeAddr, otherAddr] 
  = event.GetTriple();
// `subscriptionElementAddr` - это sc-адрес начального 
// или конечного sc-элемента (прослушивать его в sc-событии).
// `edgeAddr` - это sc-адрес сгенерированного sc-ребра 
// в "subscriptionElementAddr" или из "subscriptionElementAddr".
// `otherAddr` - это sc-адрес начального или конечного sc-элемента 
// из `edgeAddr`.
...
      </code></pre>
    </td>
  </tr>

  <tr>
  <td><strong>ScEventBeforeEraseConnector</strong></td>
  <td>
    <scg src="../images/events/sc_event_connector.gwf"></scg>
    <strong>Пример кода на C++</strong>:
    <pre><code class="cpp">
...
auto const [subscriptionElementAddr, connectorAddr, otherAddr] 
  = event.GetTriple();
// `subscriptionElementAddr` - это sc-адрес начального или
// конечного sc-элемента (прослушивать его в sc-событии).
// connectorAddr — это sc-адрес удаляемого sc-коннектора
// к или от subscriptionElementAddr.
// "otherAddr" - это sc-адрес начального или конечного sc-элемента 
// из `arcAddr`.
...
      </code></pre>
    </td>
  </tr>

  <tr>
    <td><strong>ScEventBeforeEraseOutgoingArc</strong></td>
    <td>
      <scg src="../images/events/sc_event_outgoing_arc.gwf"></scg>
      <strong>Пример кода на C++</strong>:
      <pre><code class="cpp">
...
auto const [subscriptionElementAddr, arcAddr, otherAddr] 
  = event.GetTriple();
// `subscriptionElementAddr` - это sc-адрес начального sc-элемента 
// (прослушивать его в sc-событии).
// `arcAddr` - это sc-адрес удаляемой исходящей sc-дуги 
// из `subscriptionElementAddr`.
// `otherAddr` - это sc-адрес конечного sc-элемента `arcAddr`.
...
      </code></pre>
    </td>
  </tr>

  <tr>
    <td><strong>ScEventBeforeEraseIncomingArc</strong></td>
    <td>
      <scg src="../images/events/sc_event_incoming_arc.gwf"></scg>
      <strong>Пример кода на C++</strong>:
      <pre><code class="cpp">
...
auto const [subscriptionElementAddr, arcAddr, otherAddr] 
  = event.GetTriple();
// `subscriptionElementAddr` - это sc-адрес конечного sc-элемента 
// (прослушивать его в sc-событии).
// `arcAddr` - это sc-адрес удаляемой входящей sc-дуги
// для `subscriptionElementAddr`.
// `otherAddr` - это sc-адрес начального sc-элемента `arcAddr`.
...
      </code></pre>
    </td>
  </tr>

  <tr>
    <td><strong>ScEventBeforeEraseEdge</strong></td>
    <td>
      <scg src="../images/events/sc_event_edge.gwf"></scg>
      <strong>Пример кода на C++</strong>:
      <pre><code class="cpp">
...
auto const [subscriptionElementAddr, edgeAddr, otherAddr] 
  = event.GetTriple();
// `subscriptionElementAddr` - это sc-адрес начального или
// конечного sc-элемента 
// (прослушивать его в sc-событии).
// `edgeAddr` - это sc-адрес удаляемого sc-ребра 
// в или из "subscriptionElementAddr".
// `otherAddr` - это sc-адрес начального или конечного sc-элемента 
// из `edgeAddr`.
...
      </code></pre>
    </td>
  </tr>

  <tr>
    <td><strong>ScEventBeforeEraseElement</strong></td>
    <td>
      <strong>Пример кода на C++</strong>:
      <pre><code class="cpp">
...
auto const [subscriptionElementAddr, _1, _2] 
  = event.GetTriple();
// `subscriptionElementAddr` - это sc-адрес удаляемого 
// sc-элемента 
// (прослушивать его в sc-событии).
// `_1` - пустой sc-адрес.
// `_2` - пустой sc-адрес.
...
      </code></pre>
    </td>
  </tr>

  <tr>
    <td><strong>ScEventBeforeChangeLinkContent</strong></td>
    <td>
      <scg src="../images/events/sc_event_content.gwf"></scg>
      <strong>Пример кода на C++</strong>:
      <pre><code class="cpp">
...
auto const [subscriptionElementAddr, _1, _2] 
  = event.GetTriple();
// `subscriptionElementAddr` - это sc-адрес sc-ссылки 
// с изменяемым содержимым.
// (прослушивать его в sc-событии).
// `_1` - пустой sc-адрес.
// `_2` - пустой sc-адрес.
...
      </code></pre>
    </td>
  </tr>
</table>

!!! примечание
    Все описанные методы класса `ScElementaryEvent` являются общедоступными и доступны из дочерних классов.
    
### **ScEventAfterGenerateConnector**

`ScEventAfterGenerateConnector` - это класс, который представляет sc-событие генерации sc-коннектора к или из указанного sc-элемента.

#### **GetConnector**

Метод `GetConnector` возвращает сгенерированный sc-коннектор к или из прослушиваемого sc-элемента (sc-элемент подписки).
```cpp
...
ScAddr const connectorAddr = event.GetConnector();
...
```

#### **GetConnectorType**

Он возвращает sc-тип сгенерированного sc-коннектора.

```cpp
...
ScType const connectorType = event.GetConnectorType();
...
```

#### **GetConnectorIncidentElements**

Sc-коннектором может быть sc-дуга или sc-ребро, поэтому метод `GetConnectorIncidentElements` возвращает сразу два инцидентных элемента для сгенерированного sc-коннектора.

```cpp
...
auto const [elementAddr1, elementAddr2] = event.GetConnectorIncidentElements();
...
```

### **ScEventAfterGenerateOutgoingArc** и **ScEventAfterGenerateIncomingArc**

`ScEventAfterGenerateOutgoingArc` - это класс, который представляет sc-событие генерации исходящей sc-дуги из указанного sc-элемента. `ScEventAfterGenerateIncomingArc` представляет sc-событие генерации входящей sc-дуги для указанного sc-элемента.

#### **GetArc**

Метод `GetArc` возвращает сгенерированную sc-дугу из (для `sceventaftergenerate outgoingarc`) или к (для `sceventaftergenerate Incomingarc`) прослушиваемого sc-элемента (sc-элемент подписки).

```cpp
...
ScAddr const arcAddr = event.GetArc();
...
```

#### **GetArcType**

Он возвращает sc-тип сгенерированной sc-дуги.

```cpp
...
ScType const arcType = event.GetArcType();
...
```

#### **GetArcSourceElement**

Чтобы получить начальные и конечные sc-элементы сгенерированной sc-дуги, вы можете использовать методы `GetArcSourceElement` и `GetArcTargetElement`. Для `ScEventAfterGenerateOutgoingArc` метод `GetArcSourceElement` возвращает sc-адрес прослушиваемого sc-элемента, для `Sceventaftergenerate incomingarc` метод `GetArcTargetElement` возвращает sc-адрес прослушиваемого sc-элемента.

```cpp
...
ScAddr const sourceElementAddr = event.GetArcSourceElement();
...
```

#### **GetArcTargetElement**

```cpp
...
ScAddr const targetElementAddr = event.GetArcTargetElement();
...
```

### **ScEventAfterGenerateEdge**

Этот класс представляет sc-событие добавления sc-ребра из указанного sc-элемента или к нему.

#### **GetEdge**

Метод `GetEdge` возвращает сгенерированное sc-ребро из sc-элемента или для прослушивания sc-элемента (sc-элемент подписки).

```cpp
...
ScAddr const edgeAddr = event.GetEdge();
...
```

#### **GetEdgeType**

Он возвращает sc-тип сгенерированного sc-ребра.

```cpp
...
ScAddr const edgeType = event.GetEdgeType();
...
```

#### **GetEdgeIncidentElements**

Чтобы получить инцидентные sc-элементы сгенерированного sc-ребра, вы можете использовать метод `GetEdgeIncidentElements`.

```cpp
...
auto const [elementAddr1, elementAddr2] = event.GetEdgeIncidentElements();
...
```

### **ScEventBeforeEraseConnector**

`ScEventBeforeEraseConnector` - это класс, который представляет sc-событие удаления sc-коннектора из указанного sc-элемента.

#### **GetConnector**

Метод `GetConnector` возвращает удаляемый sc-коннектор в или из прослушиваемого sc-элемента (sc-элемент подписки).

```cpp
...
ScAddr const connectorAddr = event.GetConnector();
...
```

#### **GetConnectorType**

Он возвращает sc-тип  удаляемого sc-коннектора.

```cpp
...
ScType const connectorType = event.GetConnectorType();
...
```

#### **GetConnectorIncidentElements**

Метод `GetConnectorIncidentElements` возвращает сразу два инцидентных элемента для удаляемого sc-коннектора.

```cpp
...
auto const [elementAddr1, elementAddr2] = event.GetConnectorIncidentElements();
...
```

### **ScEventBeforeEraseOutgoingArc** и **ScEventBeforeEraseIncomingArc**

`ScEventBeforeEraseOutgoingArc` is class that represents sc-event of erasing outgoing sc-arc from specified sc-element. `ScEventBeforeEraseIncomingArc` represents sc-event of erasing incoming sc-arc to specified sc-element.

#### **GetArc**

Method `GetArc` returns erasable sc-arc from (for `ScEventBeforeEraseOutgoingArc`) or to (for `ScEventBeforeEraseIncomingArc`) listen sc-element (subscription sc-element).

```cpp
...
ScAddr const arcAddr = event.GetArc();
...
```

#### **GetArcType**

It returns sc-type of erasable sc-arc.

```cpp
...
ScAddr const arcType = event.GetArcType();
...
```

#### **GetArcSourceElement**

To get source and target sc-elements of erasable sc-arc you can use `GetArcSourceElement` and `GetArcTargetElement` methods. For `ScEventBeforeEraseOutgoingArc` method `GetArcSourceElement` returns sc-address of listen sc-element, for `ScEventBeforeEraseIncomingArc` method `GetArcTargetElement` returns sc-address of listen sc-element.

```cpp
...
ScAddr const arcSourceElement = event.GetArcSourceElement();
...
```

#### **GetArcTargetElement**

```cpp
...
ScAddr const arcTargetElement = event.GetArcTargetElement();
...
```

### **ScEventBeforeEraseEdge**

This class represents sc-event of erasing sc-edge from or to specified sc-element.

#### **GetEdge**

Method `GetEdge` returns erasable sc-edge from or to listen sc-element (subscription sc-element).

```cpp
...
ScAddr const edgeAddr = event.GetEdge();
...
```

#### **GetEdgeType**

It returns sc-type of erasable sc-edge.

```cpp
...
ScAddr const edgeType = event.GetEdgeType();
...
```

#### **GetEdgeIncidentElements**

To get incident sc-elements of erasable sc-edge you can use `GetEdgeIncidentElements` method.

```cpp
...
auto const [elementAddr1, elementAddr2] = event.GetEdgeIncidentElements();
...
```

### **ScEventBeforeEraseElement**

This class represents sc-event of erasing specified listen sc-element. You can use all methods from objects of this class that are accessible from `ScElementaryEvent` class.

### **ScEventBeforeChangeLinkContent**

This class represents sc-event of changing content for listen sc-link. You can use all methods from objects of this class that are accessible from `ScElementaryEvent` class.

!!! note
    For `ScEventBeforeEraseElement` and `ScEventBeforeChangeLinkContent` method `GetTriple` returns tuple of three sc-address. The first one is a sc-event subscription sc-element. The other ones should be empty sc-addresses.

--- 

## **Frequently Asked Questions**

<!-- no toc -->
- [Is there sc-event of creating sc-node?](#is-there-sc-event-of-creating-sc-node)
- [Is fact of what happened recorded in the knowledge base? Are sc-events recorded in the knowledge base?](#is-fact-of-what-happened-recorded-in-the-knowledge-base-are-sc-events-recorded-in-the-knowledge-base)
- [Why do we need connector events?](#why-do-we-need-connector-events)

### **Is there sc-event of creating sc-node?**

A sc-event is defined as the addition, modification or erasing of connections between sc-elements, or changing link content, or erasing sc-element. This is so because knowledge is not a single sc-element, and knowledge is construction of three sc-elements at least. A sc-element does not carry any knowledge in itself. Therefore, a sc-event is considered to be emergence of some new knowledge. But there is an exception, erasing sc-elements with no connections with other sc-elements is considered an event.

Also, even if events were defined differently, it doesn't negate the fact that one cannot subscribe to sc-event of creating sc-node, since we don't know about that node in advance, because it doesn't exist.

### **Is fact of what happened recorded in the knowledge base? Are sc-events recorded in the knowledge base?**

Right now, sc-events are not recorded in the knowledge base. It will be implemented in the future versions of the sc-machine.

### **Why do we need connector events?**

Event of generating (erasing) sc-connector is needed to subscribe to both generating (erasing) sc-arc and sc-edge.

For example, man B is a brother for woman A, and man B is a brother for man C. Here, the relation `to be a brother` between woman A and man B is oriented, i.e. they are connected by a sc-arc, not by a sc-edge, and the relation `to be a brother` between man C and man B is undirected, i.e. these men are connected by a sc-edge. In order for an agent to react to appearance of both a sc-arc and a sc-edge from man B to woman A and man C correspondingly, it is necessary to subscribe that agent to appearance of a sc-connector, i.e sc-arc or sc-edge from man B.
