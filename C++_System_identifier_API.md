# ScHelper API

> **Примечание:** Это верно только для версий sc-machine, которые >= 0.10.0.

Этот API позволяет работать с системными идентификаторами sc-элементов. 
Все методы этого API являются частью класса ```ScMemoryContext```. 
Таким образом, вы можете использовать их из объекта класса ```ScMemoryContext```.

Системный идентификатор - это идентификатор, уникальный во всей базе знаний. Этот идентификатор обычно используется в исходных текстах базы знаний более низкого уровня. Для обеспечения интернационализации рекомендуется, чтобы системные идентификаторы были написаны на английском языке. Символами, используемыми в системном идентификаторе, могут быть буквы латинского алфавита, цифры, символы подчеркивания и тире. Таким образом, наиболее целесообразно сформировать системный идентификатор sc-элемента из основного английского, заменив все символы, не входящие в описанный выше алфавит, на символ “_”.

> **Примечание:** Все системные идентификаторы должны соответствовать стандартному шаблону ([a-z]|[A-Z]|'_'|'.'|[0-9])+.

> **Примечание:** Теперь только sc-узлы и sc-ссылки могут иметь системные идентификаторы.

Существует пять sc-узлов с их системным идентификатором.

![image](https://github.com/user-attachments/assets/2b35ef20-a901-4a3f-870c-d86aa7ead16c)

> **Примечание:** Чтобы подключить этот API, укажите ```#include <sc-memory/sc_memory.hpp>``` в вашем исходном файле hpp.

## SetElementSystemIdentifier

Чтобы задать системный идентификатор для некоторого sc-узла или sc-ссылки, используйте метод ```SetElementSystemIdentifier```. Если переданный системный идентификатор неверен, то метод ```SetElementSystemIdentifier``` выдаст исключение ```utils::ExceptionInvalidParams``` с описанием ошибки. Если переданный системный идентификатор уже используется для другого sc-элемента, то метод вернет значение ```false```.

```
...
ScAddr const & nodeAddr = context.GenerateNode(ScType::ConstNode);

bool const & isSystemIdentifierSet 
    = context.SetElementSystemIdentifier("my_node", nodeAddr);
// Значение `isSystemIdentifierSet` должно быть равно `true`.
...
```

Если вы хотите получить созданную пятерку, вы можете указать переменную типа ```ScSystemIdentifierQuintuple``` в качестве параметра out.

```
...
ScAddr const & nodeAddr = context.GenerateNode(ScType::ConstNode);

ScSystemIdentifierQuintuple quintuple;
bool const & isSystemIdentifierSet 
    = context.SetElementSystemIdentifier("my_node", nodeAddr, quintuple);
ScAddr const & myNodeAddr = quintuple.addr1;
// Значение `myNodeAddr` должно быть равно значению `nodeAddr`.
ScAddr const & arcToSystemIdtfLinkAddr = quintuple.addr2;
// Sc-адрес общей sc-дуги между вашим sc-узлом и 
// sc-ссылкой с системным идентификатором вашего sc-узла.
ScAddr const & systemIdtfLinkAddr = quintuple.addr3;
// Sc-адрес sc-ссылки с системным идентификатором вашего sc-узла.
ScAddr const & arcToArcToSystemIdtfLinkAddr = quintuple.addr4;
// Sc-адрес sc-дуги членства между бинарным sc-отношением 
// с системным идентификатором `nrel_system_identifier` и общей sc-дугой
// между вашим sc-узлом и sc-ссылкой с системным идентификатором вашего sc-узла.
ScAddr const & nrelSystemIdtfAddr = quintuple.addr5;
// Sc-адрес бинарной sc-связи с системным идентификатором 
// `nrel_system_identifier`.
...
```

