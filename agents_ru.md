# **C++ Agents API**

!!! примечание
    Это верно только для версий sc-machine, которые >= 0.10.0.
--- 

Этот раздел содержит подробную документацию по основным функциональным возможностям C++ Agents API, которая охватывает создание и управление агентами. В нем описывается, как описывать и создавать экземпляров агентов, а также интегрировать их в системы.

!!! примечание
    Чтобы подключить этот API, укажите "#include <sc-memory/sc_agent.hpp>` в вашем исходном файле hpp.

## **Быстрый старт**

Ознакомьтесь с нашим [**Простым руководством **](../../guides/simple_guide_for_implementing_agent.md)  о том, как быстро разработать агент на C++ с нуля.

## **Что такое агентно-ориентированная модель?**

Sc-machine реализует агентно-управляемую модель для управления знаниями, получаемыми при обработке данных. 
В агентно-ориентированной модели агенты обмениваются сообщениями только через общую память, добавление нового агента
или удаление одного или нескольких существующих агентов не приводит к изменениям в других агентах, инициализация
агентов децентрализована и чаще всего независима друг от друга.

Все агенты в рамках технологии OSTIS делятся на два класса: платформенно-независимые, т.е. реализуемые только
с помощью SC-кода, и платформенно-зависимые, реализуемые с помощью sc-machine API. Независимые от платформы агенты 
могут быть реализованы с помощью языка SCP, который интерпретируется [**scp-machine**](https://github.com/ostis-ai/scp-machine). Sc-machine представляет собой мощный, 
но простой API для разработки и поддержки платформозависимых агентов на C++.


Все агенты реагируют на появление событий в sc-памяти (sc-events). То есть агент вызывается неявно при возникновении 
sc-события, на тип которого этот агент уже подписан. Знание о том, какое sc-событие вызовет вызов этого агента 
(пробуждение этого агента), называется условием первичной инициализации. После пробуждения агент проверяет наличие 
у него условия полной инициализации. Если условие полной инициализации успешно выполнено, агент инициирует действие 
некоторого класса и начинает выполнять его с помощью программы агента 
(подробнее о действиях см. в разделе [**C++ Actions API**](actions.md)).
После выполнения своей программы агент может проверить, есть ли результат.


## **Что представляет собой спецификация агента?**

Все сведения об агенте: *первичное условие запуска*, *класс действий*, которые он может выполнять, *условие 
запуска* и *условие результата* - являются частью **спецификации агента**. Эта спецификация может быть 
представлена либо в базе знаний с использованием SC-кода, либо программно с использованием sc-machine API.


Давайте опишем спецификацию абстрактного sc-агента с вычислительной мощностью указанного набора в SCs-коде (SCg-code). 
Абстрактный sc-агент - это класс функционально эквивалентных агентов, разные экземпляры которых
могут быть реализованы разными способами. Каждый абстрактный sc-агент имеет соответствующую ему спецификацию.

```scs
// Abstract sc-agent
agent_calculate_set_power
<- abstract_sc_agent;
=> nrel_primary_initiation_condition: 
    // Класс sc-события и sc-элемента для прослушивания (подписки)
    (sc_event_after_generate_outgoing_arc => action_initiated); 
=> nrel_sc_agent_action_class:
    // Класс действий, которые должен выполнить агент
    action_calculate_set_power; 
=> nrel_initiation_condition_and_result: 
    (..agent_calculate_set_power_initiation_condition 
        => ..agent_calculate_set_power_result_condition);
<= nrel_sc_agent_key_sc_elements:
// Набор ключевых sc-элементов, используемых данным агентом
{
    action_initiated;
    action_calculate_set_power;
    concept_set;
    nrel_set_power
};
=> nrel_inclusion: 
    // Экземпляр абстрактного sc-агента; конкретная реализация агента на C++
    agent_calculate_set_power_implementation 
    (*
        <- platform_dependent_abstract_sc_agent;;
        // Набор ссылок с путями к исходным текстам программ-агентов
        <= nrel_sc_agent_program: 
        {
            [github.com/path/to/agent/sources] 
            (* => nrel_format: format_github_source_link;; *)
        };; 
    *);;

// Состояние полной инициации агента
..agent_calculate_set_power_initiation_condition
= [*
    action_calculate_set_power _-> .._action;;
    action_initiated _-> .._action;;
    .._action _-> rrel_1:: .._set;;
    concept_set _-> .._set;;
*];; 
// Агент должен проверить с помощью этого шаблона, что инициируемое действие является экземпляром 
// class `action_calculate_set_power` и что у него есть аргумент.

// Полное результативное состояние агента
..agent_calculate_set_power_result_condition
= [*
    .._set _=> nrel_set_power:: _[];;
*];;
// Агент должен проверить с помощью этого шаблона, содержит ли результат действия 
// sc-конструкцию, сгенерированную после выполнения действия.

```

<image src="../images/agents/agent_calculate_set_power_specification.png"></image>

## **Каковы способы предоставления спецификации агента?**

API sc-machine предоставляет два типа функциональных возможностей для реализации агента на C++:

* когда спецификация агента представлена в базе знаний и используется агентом;
* когда спецификация агента представлена в коде на C++ и также используется агентом.

В обоих случаях спецификация агента может быть статической, динамической или полудинамической.

1. **Спецификация статического агента** — это спецификация, предоставляемая извне в классе
агента (путем переопределения общедоступные средств получения (геттеров)). Эта спецификация не сохраняется в базе знаний, поскольку изменения
 в базе знаний не могут автоматически изменить код, указанный пользователем. Чтобы предоставить этот тип спецификации агента,
 вы можете использовать переопределение общедоступных геттеров в классе вашего агента (см. документацию ниже).

2. **Динамическая спецификация агента** - это спецификация, предоставленная в базе знаний или изначально содержащаяся в коде,
 но автоматически сохраняемая в базе знаний. После этого первый агент изменяет спецификацию второго агента, а второй агент
 использует измененную спецификацию. Чтобы реализовать этот тип спецификации, используйте API класса
 ScModule и API класса ScAgentBuilder (смотреть [**C++ Modules**](modules.md)).

3. **Полудинамическая спецификация агента** может быть получена, когда спецификация предоставляется в базе
 знаний или изначально содержится в коде и добавляется извне (через переопределяющие общедоступные средства получения(геттеры)).

Статическая спецификация агента может быть полезна, если вы впервые внедряете агент на C++ или хотите свести к минимуму количество поисков в базе знаний. Динамическая спецификация агента предоставляет возможность анализировать и изменять эту спецификацию другими агентами. Полудинамическая спецификация может быть полезна, если вы хотите изменить некоторые части этой спецификации, но при этом хотите, чтобы вызовы агента выполнялись быстро.

---

## **Спецификация статического агента**

Здесь будет рассмотрен API для реализации агента со статической спецификацией. Это самый простой вариант реализации, который вам будет понятен. Чтобы узнать о динамической спецификации агента, смотрите [**C++ Modules API**](modules.md).

Существует два основных класса, которые вы можете использовать для реализации агента: `ScAgent` и `ScActionInitiatedAgent`.

### **ScAgent**

В C++ существует базовый класс для агентов. Этот класс предоставляет реализованные методы для извлечения элементов спецификации агента из базы знаний. Все эти методы могут быть переопределены в вашем классе агента.

Вы должны различать абстрактный sc-агент как некоторый класс функционально эквивалентных sc-агентов, описанных в базе знаний, и `ScAgent` как класс C++, который реализует API для работы с абстрактными sc-агентами в базе знаний.

Этот класс можно использовать для всех типов платформозависимых агентов. Агенты этого класса реагируют на события в базе знаний, проверяют условие полной инициализации. Если проверка прошла успешно, генерируют, инициируют и выполняют действие. После этого они проверяют условие полной результативности. Пример использования этого класса представлен ниже.

```cpp
// файл my_agent.hpp
#pragma once

#include <sc-memory/sc_agent.hpp>

// Наследуйте свой класс agent от класса `ScAgent` и укажите аргумент шаблона 
// как класс sc-события. Здесь `ScEventAfterGenerateIncomingArc<
// ScType::ConstPermPosArc>` - это тип события, на которое реагирует данный агент.

class MyAgent : public ScAgent<
  ScEventAfterGenerateIncomingArc<ScType::ConstPermPosArc>>
{
public:
  // Здесь вы должны указать класс действий, которые выполняет данный агент. 
 // Здесь `GetActionClass` переопределяет `GetActionClass` 
 // в классе `ScAgent`. Это переопределение обязательно.
  ScAddr GetActionClass() const override;
 // Здесь вы должны реализовать программу данного агента. 
 // Это переопределение обязательно.
  ScResult DoProgram(
    ScEventAfterGenerateIncomingArc<
      ScType::ConstPermPosArc> const & event, 
    ScAction & action) override;

  // Другие методы, определяемые пользователем.
};
```

Вы не можете переопределить `DoProgram` без аргумента sc-event. Можно переопределить один из этих методов.

```cpp
// файл my_agent.hpp
#pragma once

#include <sc-memory/sc_agent.hpp>

class MyAgent : public ScAgent<
  ScEventAfterGenerateIncomingArc<ScType::ConstPermPosArc>>
{
public:
  ScAddr GetActionClass() const override;
  ScResult DoProgram(ScAction & action) override;

  // Другие методы, определяемые пользователем.
};
```

Смотрите [**C++ Events API**](events.md) и [**C++ Event subscriptions API**](event_subscriptions.md), чтобы узнать, как использовать sc-события и обрабатывать их.

!!! примечание
    Определите класс действий как ключевой узел в классе агентов или классе ключевых узлов.

!!! предупреждение
    Вам следует переопределить методы `GetActionClass` и `DoProgram`. Но если вы предоставите спецификацию вашего агента в базе знаний, то вам не нужно будет переопределять `GetActionClass`. Смотрите [**C++ Modules API**](modules.md), чтобы узнать, как реализовать агенты со спецификацией в базе знаний.

Вы можете указать любые существующие типы событий в качестве аргумента шаблона для класса `ScAgent`. Например, вы можете сгенерировать агент, который будет запускаться при sc-событии удаления sc-элемента.

```cpp
// файл my_agent.hpp
#pragma once

#include <sc-memory/sc_agent.hpp>

class MyAgent : public ScAgent<ScEventBeforeEraseElement>
{
public:
  ScAddr GetActionClass() const override;
  ScResult DoProgram(
    ScEventBeforeEraseElement const & event, ScAction & action) override;

 // Другие методы, определяемые пользователем.
};
```

Если вы хотите изменить спецификацию этого агента в базе знаний, то напишите следующим образом:

```cpp
// файл my_agent.hpp
#pragma once

#include <sc-memory/sc_agent.hpp>

class MyAgent : public ScElementaryEventAgent
{
public:
  ScAddr GetActionClass() const override;
  ScResult DoProgram(
    ScElementaryEvent const & event, ScAction & action) override;

   // Другие методы, определяемые пользователем.
};
```

Эта реализация позволяет запрограммировать любой тип sc-события в "DoProgram`.

!!! примечание
    `ScElementaryEventAgent` - это псевдоним для "ScAgent<элементарное событие>`.

### **ScActionInitiatedAgent**

В мультиагентных системах большинство агентов реализованы для выполнения действий, инициированных другими агентами. Хотя `ScAgent` полезен для создания общей логики обработки событий, его использование для обработки инициализации действий требует некоторого шаблона. Мы внедрили еще один класс агентов, чтобы нашим пользователям было проще внедрять агенты, выполняющие действия. Для реализации этих агентов требуется передать узел класса действий, а не проверять условие запуска вручную.

Этот класс может использоваться только для агентов, которые должны запускаться путем генерации исходящей sc-дуги из узла класса `action_initiated`.

```cpp
// файл my_agent.hpp
#pragma once

#include <sc-memory/sc_agent.hpp>

// Наследуйте свой класс agent от класса `ScActionInitiatedAgent`.
class MyAgent : public ScActionInitiatedAgent
{
public:
 // Здесь вы должны указать класс действий, которые выполняет данный агент. 
 // Это переопределение обязательно.
  ScAddr GetActionClass() const override;
  // Здесь вы должны реализовать программу данного агента. 
  // Это переопределение обязательно.
  ScResult DoProgram(
    ScActionInitiatedEvent const & event, ScAction & action) override;
  // Здесь `ScActionInitiatedEvent` - это тип события, на которое реагирует 
  // данный агент.

  // Другие методы, определяемые пользователем.
};
```

!!! примечание
    `ScActionInitiatedAgent` имеет значение по умолчанию `GetInitiationConditionTemplate`, которое возвращает шаблон, который можно использовать для проверки того, что инициированное действие является действием с классом указанного агента.

!!! примечание
    `ScActionInitiatedEvent` - это псевдоним для `ScEventAfterGenerateOutgoingArc<ScType::ConstPermPosArc>` с элементом sc подписки `action_initiated`.

---

### **Required methods**

#### **GetActionClass**

It gets action class that the agent of specified class performs. If the abstract sc-agent for this agent class does not have an action class, then method will throw `utils::ExceptionItemNotFound`.

```cpp
// File my_agent.cpp
#include "my_agent.hpp"

#include "keynodes/my_keynodes.hpp"

ScAddr MyAgent::GetActionClass() const
{
  // You must specify valid sc-address of action class and you should 
  // provide action class that is include to the one of types: 
  // `receptor_action`, `effector_action`, `behavioral_action` or 
  // `information_action`.
  // In other case, the given sc-agent can’t be subscribed to sc-event.
  return MyKeynodes::my_action;
}
```

See [**C++ Keynodes API**](keynodes.md) and learn how to define keynodes and use them for your agent.

#### **DoProgram**

This method is executed when agent checked initiation condition successfully. Using program, the agent processes an input construction and generates an output construction.
Each agent performs action with the help of its program.

```cpp
ScResult MyAgent::DoProgram(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  // Class `ScAction` encapsulates information about sc-action. The provided 
  // action is action that the given agent performs right now. 
  // It belongs to `MyKeynodes::my_action` class.
  // If your agent inherits `ScActionInitiatedAgent` class then this agent
  // performs action initiated externally. If your agent inherits `ScAgent`
  // then this agent generates action, initiates and performs new action, not
  // provided externally.
  // Actions are copyable and movable. `ScAction` is inherited from `ScAddr`.

  // `ScActionInitiatedEvent` class is event type on which the given agent 
  // is triggered. It is encapsulate information about sc-event. 
  // The provided event is event on which the agent is triggered right now. 
  // It has methods to get information about initiated sc-event: `GetUser`, 
  // `GetArc`, `GetSubscriptionElement`, `GetArcSourceElement`.
 
  // Implement logic of your agent...

  // You must specify action state in all ends of your agent program. 
  // `FinishSuccessfully` sets action as `action_finished_successfully`.
  // You can’t generate object of `ScResult` via constructor, because it is 
  // private.
  return action.FinishSuccessfully(); 
}
```

`ScAgent` class has field `m_context`. It is an object of `ScAgentContext` class. You can use to complete operations in sc-memory. See [**C++ Agent context API**](agent_context.md) to learn more about accessible methods.

!!! warning
    If you don't catch sc-exceptions in `DoProgram` then sc-machine will catch them and will finish action with error and will warn you about it.

##### Handling action arguments

There are many ways of methods that get action arguments. Use them, they can help you to simplify code.

```cpp
ScResult MyAgent::DoProgram(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  auto [argAddr1, argAddr] = action.GetArguments<2>();

  // Some logic...

  return action.FinishSuccessfully(); 
}
```

```cpp
ScResult MyAgent::DoProgram(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  ScAddr const & argAddr1 = action.GetArgument(ScKeynodes::rrel_1);
  // Parameter has ScAddr type.

  // Some logic...

  return action.FinishSuccessfully();
}
```

```cpp
ScResult MyAgent::DoProgram(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  ScAddr const & argAddr1 = action.GetArgument(1); // size_t
  // It would be the same if we pass ScKeynodes::rrel_1 instead of 1.

  // Some logic...

  return action.FinishSuccessfully();
}
```

```cpp
ScResult MyAgent::DoProgram(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  ScAddr const & argAddr1 
    = action.GetArgument(1, MyKeynodes::default_text_link);
  // If action hasn’t the first argument, then MyKeynodes::default_text_link 
  // will return.

  // Some logic...

  return action.FinishSuccessfully();
}
```

```cpp
ScResult MyAgent::DoProgram(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  // You can use object of ScAction as object of ScAddr.
  ScIterator3Ptr const it3 = m_context.CreateIterator3(action, ..., ...);

  // Some logic...

  return action.FinishSuccessfully();
}
```

##### Handling action result

```cpp
ScResult MyAgent::DoProgram(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  // Some logic...
 
  action.FormResult(foundAddr1, generatedAddr1, ...); 
  // Or you can use `UpdateResult` method.
  return action.FinishSuccessfully();
}
```

To learn more about methods of `ScAction` class, see [**C++ Actions API**](actions.md).

```cpp
ScResult MyAgent::DoProgram(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  // Some logic...
 
  action.SetResult(structureAddr);
  return action.FinishSuccessfully();
}
```

##### Handling action finish state

```cpp
ScResult MyAgent::DoProgram(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  // Some logic...

  if (/* case 1 */)
    return action.FinishSuccessfully();
  else if (/* case 2 */)
    return action.FinishUnsuccessfully();
  else
    return action.FinishWithError();
}
```

```cpp
ScResult MyAgent::DoProgram(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  action.IsInitiated(); // result: true
  action.IsFinished(); // result: false
  action.IsFinishedSuccessfully(); // result: false

  // Some logic...

  return action.FinishSuccessfully();
}
```

!!! warning
    The API of `ScAction` provides other methods. Don't use `GetResult` for initiated but not finished action and `Initiate` for initiated or finished action.

---

### **Not required methods**

#### **GetAbstractAgent**

It searches abstract agent for agent of specified class. If the agent implementation for this agent class is not included in any abstract sc-agent, then `GetAbstractAgent` will throw `utils::ExceptionItemNotFound`.

You can override this method in your agent class.

```cpp
ScAddr MyAgent::GetAbstractAgent() const
{
  // You must specify valid sc-address of abstract agent. 
  // In other case, the given sc-agent can’t be subscribed to sc-event.
  return MyKeynodes::my_abstract_agent;
}
```

!!! warning
    Remember, if you override only this method and required methods, then other getters will return elements of specification for specified abstract agent. All not override getters call `GetAbstractAgent`. 

#### **GetEventClass**

It searches the sc-event class to which the agent class is subscribed. It will throw `utils::ExceptionItemNotFound` if the abstract sc-agent for this agent class does not have primary initiation condition.

You can override this method in your agent class.

```cpp
ScAddr MyAgent::GetEventClass() const
{
  // You must specify valid sc-address of event class. 
  // In other case, the given sc-agent can’t be subscribed to sc-event.
  return ScKeynodes::sc_event_after_generate_outgoing_arc;
}
```

#### **GetEventSubscriptionElement**

This method searches sc-event subscription sc-element for which sc-event initiates. It will throw `utils::ExceptionItemNotFound` if the abstract sc-agent for this agent class does not have primary initiation condition.

You can override this method in your agent class.

```cpp
ScAddr MyAgent::GetEventSubscriptionElement() const
{
  // You must specify valid sc-address of sc-event subscription sc-element. 
  // In other case, the given sc-agent can’t be subscribed to sc-event.
  return ScKeynodes::action_initiated;
}
```

!!! warning
    Don't override `GetEventClass` and `GetEventSubscriptionElement` for agent with statically specified sc-event type. Your code can't be compiled. Override them, if your agent class inherit `ScAgent<ScElementaryEvent>` (`ScElementaryEventAgent`).

#### **GetInitiationCondition**

It gets initiation condition for agent of this class. Initiation condition is a sc-template (sc-structure) that is used by the agent to check necessary connections between sc-elements of sc-event triple and sc-elements in the knowledge base before the agent performs an action. This method will throw `utils::ExceptionItemNotFound` if the abstract sc-agent for this agent class does not have initiation condition.

You can override this method in your agent class.

```cpp
ScAddr MyAgent::GetInitiationCondition() const
{
  // You must specify valid sc-address or sc-template of initiation condition. 
  // In other case, the given sc-agent can’t be called.
  return ScKeynodes::my_initiation_condition;
}
```

```cpp
// sc_keynodes.hpp
...
// Here you should specify template to check initiation condition before 
// starting agent.
static inline ScTemplateKeynode const & my_initiation_condition
  = ScTemplateKeynode(my_initiation_condition)
    // You must specify valid sc-address of initiation condition. Otherwise, 
    // the given sc-agent can’t be called. For sc-event of generating (erasing) 
    // sc-connector (sc-arc or sc-edge), you must specify in template of 
    // initiation condition a triple in place of which agent have to substitute 
    // sc-elements involved in initiated event. These elements will be the 
    // sc-connector (sc-arc or sc-edge) and its incident sc-elements. 
    // For sc-event of changing sc-link content (or erasing sc-element), you 
    // should use sc-link (sc-element) in template of initiation condition 
    // explicitly. Otherwise your agent will not be called because its 
    // initiation condition is fulfilled for the action of another agent.
    .Triple(
      ScKeynodes::action_initiated,
      ScType::VarPermPosArc,
      ScType::VarNode >> "_action"
    )
    // After you should specify triples that only apply to your agent.
    .Triple(
      MyKeynodes::my_action,
      ScType::VarPermPosArc,
      "_action"
    )
    .Quintuple(
      "_action",
      ScType::VarPermPosArc,
      ScType::VarNode,
      ScType::VarPermPosArc,
      ScKeynodes::rrel_1
    );
...
```

!!! warning
    Agent must have initiation condition template with only one triple that can be used to substitute sc-elements involved in initiated event, otherwise agent will not started performing sc-action.

#### **GetInitiationConditionTemplate**

You can specify the initiation condition template in code rather than in the knowledge base. It can be useful if you want to quickly check agent initiation condition without loading the template into the knowledge base.

```cpp
ScTemplate MyAgent::GetInitiationConditionTemplate(
  ScActionInitiatedEvent const & event) const
{
  // In this method, you shouldn't specify triple for sc-event sc-elements.
  // You should specify triples that only apply to your agent.
  initiationCondition.Triple(
    MyKeynodes::my_action,
    ScType::VarPermPosArc,
    event.GetOtherElement() // It returns sc-action.
  );
  return initiationCondition;
}
```

#### **CheckInitiationCondition**

For speed, you can implement the agent initiation condition in the form of checks on iterators.

```cpp
bool MyAgent::CheckInitiationCondition(ScActionInitiatedEvent const & event)
{
 // ScActionInitiatedEvent is event type on which the given agent triggered. 
 // It is encapsulate information about sc-event. The provided event is event 
 // on which the agent is triggered right now. It has methods to get 
 // information about initiated sc-event: GetUser, GetArc, 
 // GetSubscriptionElement, GetArcSourceElement, GetArcTargetElement.
 // All events are not copyable and movable.
 return m_context.CheckConnector(
   ScType::ConstPermPosArc, 
   MyKeynodes::my_action, 
   event.GetArcTargetElement());
}
// You can use all opportunities of sc-memory API to check initiation 
// condition of your agent.
```

!!! note
    Each of these methods will be called before `DoProgram` call.

!!! warning
    You can specify only one of methods: `GetInitiationCondition`, `GetInitiationConditionTemplate` and `CheckInitiationCondition`. Otherwise, your code can't be compiled.

#### **GetResultCondition**

It gets result condition for agent of this class. Result condition is a sc-template (sc-structure) that is used by the agent to check necessary connections between sc-elements of sc-event triple and sc-elements in the knowledge base after the agent has performed an action. This method will throw `utils::ExceptionItemNotFound` if the abstract sc-agent for this agent class does not have result condition.

You can override this method in your agent class.

```cpp
ScAddr MyAgent::GetResultCondition() const
{
  // Here you should specify template to check action result content.
  // You must specify valid sc-address or sc-template of result condition. 
  // In other case, the given sc-agent can’t be finished successfully.
  return ScKeynodes::my_result_condition;
}
```

```cpp
// sc_keynodes.hpp
...
// Here you should specify template to check action result content.
static inline ScTemplateKeynode const & my_result_condition
  = ScTemplateKeynode(my_result_condition)
    .Triple(
      MyKeynodes::my_class,
      ScType::VarPermPosArc,
      ScType::VarNode
    );
    // This template is used to check that agent generated sc-arc
    // between `MyKeynodes::my_class` and some sc-node.
...
```

#### **GetResultConditionTemplate**

You can specify the result condition template in code rather than in the knowledge base.

```cpp
ScTemplate MyAgent::GetResultConditionTemplate(
  ScActionInitiatedEvent const & event, ScAction & action) const
{
  // Here you should specify template to check action result content.
  ScTemplate resultCondition;
  resultCondition.Triple(
    MyKeynodes::my_class,
    ScType::VarPermPosArc,
    ScType::VarNode
  );
  return resultCondition;
}
```

!!! note
    `GetResultCondition` and `GetResultConditionTemplate` are used to search sc-constructions in action result only.

#### **CheckResultCondition**

For speed, you can implement the agent result condition in the form of checks on iterators.

```cpp
bool MyAgent::CheckResult(
  ScActionInitiatedEvent const & event, ScAction & action)
{
  ScStructure const & actionResult = action.GetResult();
  ScIterator3Ptr const it3 = m_context.CreateIterator3(
    MyKeynodes::my_class,
    ScType::ConstPermPosArc,
    ScType::ConstNode,
    ScType::ConstPermPosArc,
    actionResult
  );
  return it3->Next() 
    && actionResult.HasElement(MyKeynodes::my_class) 
    && actionResult.HasElement(it3->Get(2))
    && !it3->Next() // Check that action result has no more triples.
    && m_context.GetOutputArcsCount(actionResult) == 3;
}
```

!!! note
    Each of these methods will be called after `DoProgram` call.

!!! warning
    You can specify only one of methods: `GetResultCondition`, `GetResultConditionTemplate` and `CheckResultCondition`. Otherwise, your code can't be compiled.

--- 

## **Frequently Asked Questions**

<!-- no toc -->
- [Is it possible to subscribe an agent for more than one sc-event?](#is-it-possible-to-subscribe-an-agent-for-more-than-one-sc-event)
- [What happens if I don't specify full initiation condition in agent class?](#what-happens-if-i-dont-specify-full-initiation-condition-in-agent-class)
- [Can there be an agent without primary initiation condition?](#can-there-be-an-agent-without-primary-initiation-condition)
- [Is it possible to generate object of `ScAgent` class and call any of class methods?](#is-it-possible-to-generate-object-of-scagent-class-and-call-any-of-class-methods)
- [Is agent's call protocol preserved?](#is-agents-call-protocol-preserved)
- [What is advisable to do as an agent and what is not?](#what-is-advisable-to-do-as-an-agent-and-what-is-not)
- [What's the purpose of result condition? What is it used for?](#whats-the-purpose-of-result-condition-what-is-it-used-for)

### **Is it possible to subscribe an agent for more than one sc-event?**

Future versions of the sc-machine will implement complex sc-events. Complex sc-events will be a set of elementary events. It will be possible to subscribe agents to such sc-events.

If it is a question of whether it is possible to subscribe the one and the same agent to two different elementary sc-events, it is better not to do it. Each agent should be subscribed to only one type of sc-event. If you need to subscribe an agent to multiple types of sc-events, you're probably doing something wrong.

Consider an example. Suppose you want to implement an agent to recalculate power of a set, which will react when a sc-arc is removed from the set and when a sc-arc is added from it. You should not do such an agent. It is better to split these two logics into two independent objects: an agent that will react when a sc-arc is erased from the specified set and an agent that will react when a sc-arc is generated from this set.

### **What happens if I don't specify full initiation condition in agent class?**

We allow not specifying full initiation condition for agents. However, remember, your agents will be executed every time an event occurs over the listen sc-element your agent is subscribed to.

### **Can there be an agent without primary initiation condition?**

No, agents can't be without primary initiation condition. Agents react to events in sc-memory. A primary initiation condition indicates which sc-event agent is subscribed to, i.e. when agent will be called.

### **Is it possible to generate object of `ScAgent` class and call any of class methods?**

Yes, you can call a method of agent class through object of that class. But remember that you can't generate necessary arguments for all methods.

### **Is agent's call protocol preserved?**

Until the sc-machine implements functionality for logging processes in sc-memory. It will be added in the next versions. However, the sc-machine does not remove anything by itself.

### **What is advisable to do as an agent and what is not?**

It depends on your goal. If you want to make an ostis-system, then the whole functionality of your system should be a collective of agents. If you want to use a machine as a database to store and process information in a convenient and simple way, not everything should be agents. To improve performance, use agents with static specification or without specification, combine multiple agents into one, optimize sc-memory requests and etc.

### **What's the purpose of result condition? What is it used for?**

Now, it has no effect on agent finishing. But in the future it can be used for verification and debugging of agents' work.
