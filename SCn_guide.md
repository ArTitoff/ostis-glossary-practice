# Целью данной работы являляется помочь студентам перового курса с выполнением практики.

Материалы для выполнения работы (установка OSTIS и прочее) находятся [ЗДЕСЬ](https://github.com/iis-32170x/RPIIS/tree/main/sem2#практика) (нужно пролистать чуть ниже)
## 1.Выбор области 

Перед выполнением нужно выбрать [в таблице](https://docs.google.com/spreadsheets/d/1xcR9IC5qsCP4gtq0eL4nDYyJ7BdNZp_Fi16MQDGd4e4/edit#gid=2081981130) предметную область (пункт), который вы хотите формализовать, и записаться туда.  

## 2.Поиск информации

Далее нужно найти информацию/статьи/научные работы из интернета или других известных вам источников, которая будет связана с выбранной вами областью. Из источником можем посоветовать вам [Google Scholar](https://scholar.google.com/) , где по вашему запросу будут найдены различные статьи и научные работы.


![image](https://github.com/iis-32170x/RPIIS/assets/147609793/1c8bfd3c-7cb4-4220-8ce6-8b8b25376276)

***

### Пример запроса

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/6f14141b-8843-4397-ab8a-f5797740863f)


***

Просматривая статьи, нужно искать новые понятия, которых нет в стандарте. Для того чтобы проверить, есть ли найденное вами понятие в стандарте, можно использовать регулярные выражения из 4 лабораторной 1 семестра. (добавить скрин применения регулярных выражений и ссылку на стандарт с которым сравниваем, а то их несколько)


***


## 3.Что делать после нахождения понятия?

После нахождения понятия, которого нет в стандарте, нужно формализовать его на LaTeX


- Шаблон для практики доступен [по ссылке](https://www.overleaf.com/read/cjtjbmnbgqrh#6a9cb3). 


### В шаблоне по ссылке нужно перейти в MENU (слева-сверху)

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/f7fac312-3ebc-4dc6-8001-826aff8aba3c)

***
 
### Далее нажать Copy Project
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/ea4506b7-5a51-45e0-a340-0c51fc621a9b)

***

### Ввести название вашего проекта и нажать Copy

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/94d87139-746e-4eed-a032-da308d27cacb)

***

### Таким образом у вас создасться ваш проект, с вашим названием. Для просмотра всех проектов можно нажать на домик

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/ba88dc22-a367-44b4-9b9e-ef2141f14fca)

### И выбрать нужный вам проект

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/846d82b0-6f47-4ae6-a7f6-1eaf28747573)


***




В проекте уже присутствуют все нужные вам файлы, давайте рассмотрим те, что пригодятся вам для работы:

### В файле title.tex нужно поменять ФИО на свое

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/bd22f830-c0b0-4982-b1dd-cf67ca9a0da1)

***

### Для удобства работы и целостности проекта понятия следует описывать в отдельных фалах. Чтобы создать новый файл, нужно нажать на листик в левом-верхнем углу. Назвать файл и создать его. В проекте для удобства поиска все такие файлы названы "section.." 
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/78bc53fe-b007-42b9-8330-904037f189f2)

***
### После создания ваш файл появится в проекте

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/11a8fce8-7e32-427f-93a2-c365e4876d46)

***
### Content.tex - это основной файл для подключения других файлов, в которых вы формализовали понятия. Подключить файл можно при помощи команды \input{_название вашего файла_}

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/7115da28-d7c0-42b8-a73e-edc51a50b346)


***
# Итак, рассмотрим пример формализации [этой статьи](http://www.energystrategy.ru/projects/Energy_21/7-6.pdf),  убедившись, что в статье не будут встречаться понятия, уже описанные в стандарте

### Сразу же на первой странице нам попадается понятие ИТ-инфраструктура, опишем его:

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/983cea8b-94fb-4ce3-8e62-bebe633e02db)

***

### Следует напомнить, что для каждого понятия нужно стараться описать его синонимы, авторов, библиографические источники, пояснения, примеры.
### В данной статье не очень много синонимов, поэтому можно поискать в других источниках.
### В интернете получилось найти статью, в которой описано это понятие. Можно приступить к формализации.

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/acd99478-21f1-4c74-adf4-6570e1b975a9)

***
### Для этого в проекте создаем новый файл (в примере он называется test1). 
### Для формализации в SCn нужно прописать следующее:

```
\begin{SCn}
\begin{small}


\end{small}
\end{SCn}
```

### Внутри мы при помощи \scnheader{*понятие*} мы записываем название понятия ИТ-инфраструктура, а при помощи \scnidtf{*синоним*} - синоним или текст, которым можно заменить данное понятие. (результат отображается справа)
```
\scnheader{ИТ-инфраструктура}
\scnidtf{система организационных структур и подсистем, обеспечивающих функционирование и развитие чего-либо}
```
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/be218f5b-3451-4e59-bc22-c441e48d242a)
***

### Далее в статье идут основные составляющие в ИТ-инфраструктуре

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/b89af26b-13b1-4f64-86ed-e33648df4007)
***

### Тут следует рассказать про разницу между разбиением и декомпозицией(обобщенной декомпозицией). Если просто - разбиение используется, когда мы точно знаем, что элементы разбиения не связаны/пересекаются между собой и в разбиении перечислены все элементы.Если вы не уверены - лучше использовать декомпозицию. Подробнее ниже:

* Декомпозиция - это тогда, когда вы не можете точно сказать пересекаются ли подмножества или нет.
* Разбиение - частный случай декомпозии, когда все подмножества исходного множества не пусты, попарно не пересекаются и в результате объединения дают исходное множество
* Декомпозицию можно использовать для множеств и сущностей
* Разбиение только для множеств
* Обобщённая декомпозиция - частный случай декомпозиции, используется в основном только для сущностей
 


### Есть несколько способов оформления разбиения и декомпозиции
### Мы можем задать название разбиению или декомпозиции. Если такого названия нет, то можно оформить так (только для разбиения):

```
\begin{scnsubdividing}
    \scnitem{  }
\end{scnsubdividing}
```
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/c5b0233e-5ac1-4580-bc8b-8c09e3e3b8e0)

***

### Или так (в данном случае в скобках {} нужно указать разбиение или декомпозиция) :

```
\begin{scnrelfromset}{разбиение}
    \scnitem{  }
\end{scnrelfromset}
```
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/ef806c85-d3bd-44cd-9943-044c105e6f31)
***

### В нашем же случае разбиение можно назвать "*основные составляющие*". Благодаря слову "*основные*", можно догадаться, что мы имеем дело с декомпозицией, так как если есть основные, значит есть еще какие-то другие, что указывает на неполноту множества.
### Таким способом можно оформить декомпозицию или разбиение с названием:

```
\scnrelfrom{обобщенная декомпозиция}{основные составляющие}
\begin{scnindent}
    \begin{scneqtoset}
        \scnitem{ }
    \end{scneqtoset}
\end{scnindent}
```

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/48a70a82-e4a4-42f6-a3f9-a033bfada0f2)


***
### В нашем случае в статье для каждого элемента этой декомпозиции есть свое пояснение/примечание (в зависимости от контекста).
### Опишем её при помощи такой команды:

```
\begin{scnindent}
\scntext{пояснение}{текст пояснения}
\end{scnindent}
```

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/64138dd8-ae19-45f2-b5b8-5d26edef8fff)

***
### Добавим для остальных:

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/eb55d687-d2a8-4a44-9636-150750a08b55)


***

### Далее пишется, что к этой же инфраструктуре могут быть отнесены автоматизированные системы управления энергетическими объектами

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/38a3fdaf-cf7e-4447-958a-3ac19f031bad)
***

### В данном случае можно оформить в виде разбиения, так как перечислены все компоненты, которые не пересекаются

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/b709a848-565a-47f4-a605-ffebf00a5f69)

***

### Для данного понятия так же нужно указать авторов и библиографические источники(ссылки).

### В нашем случае авторами будут авторы статьи, а источником - сама статья и сайт, с которого мы брали определение
***

### Библиографические источники(ссылки) должны быть записаны по следующей схеме:

### Допустим есть книга «От многоагентных систем к интеллектуальным организациям: философия, психология, информатика». Написал ее Тарасов В.Б. в 2002г. 

### 1. Сначала рабоатем с её названием:
### У первого слова в названии берем первые 5 букв, первая буква в верхнем регистре(с большой буквы)

### (!!!в данном случае за слова не считаются предлоги, местоимения, союзы, частицы и т.д.) 
### Если в названии попадается что-то из вышеперечисленного, в название записывается только первая буква с нижним регистром

### В нашем случае первым идет предлог "от". Записываем только первую букву в нижнем регистре
```
о
```
### Следующим идет слово "многоагентных", это наше первое слово. Из него мы берем первые пять букв, первая буква в верхнем регистре, и добавляем к уже имеющемуся названию. Без пробелов

```
оМного
```

### Следующим идет слово "систем". У последующих слов нужно брать только первую букву в верхнем регистре. Получим:

```
оМногоС
```
### Нужно отметить, что количество букв с верхним регистром не должно превышать 5. Как только набирается 5 заглавных букв, работа с названием заканчивается, и мы переходим к пункту 2.
### Если в названии меньше 5 букв, оставляем как есть (такой пример будет разобран дальше)
### Следующим идет предлог "к". Добавляем его к названию в нижнем регистре

```
оМногоСк
```

### Далее идет слово "интеллектуальным". Добавляем к названию "И"
```
оМногоСкИ
```
### Затем слово "организациям". Добавляем "О"
```
оМногоСкИО
```
### Знаки препинания просто пропускаются. В нашем случае пропускаем ":"

### Добавляем слово "философия"
```
оМногоСкИОФ
```
### Теперь в названии присутствует 5 бук в верхнем регистре. После этого слова в сокращенное название не добавляются

### 2. Далее без пробелов добавляется "-" и год написания/издания. В нашем случае 2002г.
```
оМногоСкИОФ-2002
```
### Следует обратить внимание, что после года не ставится "г." (год) и прочее

### 3. После без пробелов нужно добавить сокращенное название формата ресурса. Например статья - ст, книга - кн, электронный ресурс - эр, научаня публикация - нп
### В нашем случае это книга - кн
```
оМногоСкИОФ-2002кн
```

### 4. Перед названием добавляется фамилия автора и инициалы. После фамилли ставится пробел, а инициалы добавляются без пробелов  
```
Тарасов В.Б.оМногоСкИОФ-2002кн
```

### Если авторов несколько, пишется только первый автор, а после его инициалов ставится точка

```
Тарасов В.Б..оМногоСкИОФ-2002кн
```
### Но в данном случае всего один автор, поэтому сокрашенное название книги преобретает вид: ㅤㅤㅤㅤ _Тарасов В.Б.оМногоСкИОФ-2002кн_

**** 

### Рассмотрим название статьи, из которой было взято понятие. "Облачная" структура энергоинформационных систем Массель Л.В., Копайгородский А.Н., Грибова В.В. 2020г.

### Первое слово облачная, выписываем первые 5 букв
```
Облач
```
### Далее выписываем первые буквы слов "структура", "энергоинформационных", "систем"
```
ОблачСЭС
```
### В названии оказалось меньше 5 символов в верхнем регистре. В таком случае нужно просто оставить все как есть

### Добавляем год и статья - ст
```
ОблачСЭС-2020ст
```
### У данной статьи 3 автора, но записать нужно только ФИО первого автора и поставить точку
```
Массель Л.В..ОблачСЭС-2020ст
```
***
### Вторая статья: ИТ-инфраструктура предприятия (Информационно-технологическая инфраструктура предприятия) 2022г.
### Выписываем первые пять букв и остальные слова

```
ИнфорТИП
```
### Добавляем год и тип источника (электронный ресурс - эр)
```
ИнфорТИП-2022эр
```
### Если, как в этом случае, у источника нет автора, он просто не записывается. Получаем _ИнфорТИП-2022эр_
***

### Теперь источники можно добавить в проект
### Пример оформления, если у нас всего один автор или источник:
```
\scnrelfrom{автор}{ФИО автора}
\scnrelfrom{библиографический источник}{название библиографического источника }
```

### Пример оформления, если у нас несколько авторов или источников:
```
\begin{scnrelfromlist}{библиографическая ссылка}
    \scnitem{название библиографического источника }
    \scnitem{   }
\end{scnrelfromlist}
```

### В нашем случае придется использовать второй вариант:

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/41a73e4f-8f09-4f4d-943f-79b7807f1840)


***
### Далее встречается понятие "информационный поток"

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/2c22467f-14fa-4982-beb2-99b277d3dd6c)

*** 
### Для его описания создаем новый файл

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/2023a9b1-b645-4f27-b997-f875d805b7ff)

### И добавляем его в content.tex при помощи команды \input{*название файла*}. Чтобы начать описывать с новой строницы, нужно ввести \newpage

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/976d101e-b029-449d-9003-5b0c87620035)


### В этой статье мало что сказано про "информационный поток", поэтому придется искать информацию в других источниках

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/24f75da0-a570-4bb7-bf82-e5203280ac63)

***
### В источнике указано, что информационный поток входит в контекст теории информации, т.е. является подмножеством

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/b951049f-ff1f-4109-b89a-a26daed9b253)

### Оформим это для нашего понятия:

```
\scnsubset{cтрогое подмножество --- <<включено в>>}
```

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/fbfff318-8104-4deb-9bcf-5f67a41b3477)

***

### Если у понятия есть пример, который нужно оформить: изображение, текст и т.д. Для этого можно сделать следующее:

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/1cb913cc-5c0c-4f3e-ba24-b17a4bac8cab)


### Оформление текста

```
\scnhaselementrole{пример}{какой-то пример}
```

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/dd4a52cb-d3e7-4e4b-955e-5b490fad8092)

***

### Для оформления изображения нужно создать папку, в которой будут находится эти изображения

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/c5f57195-7801-4424-aa13-d454040f0b6c)

***
### После создания, переходим в папку и нажимаем Upload. Затем загружаем изображение.
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/859b00a2-c53f-49a4-9e1b-17d04d072863)

***
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/b5c17b9f-94a8-4663-901d-9ad2dcb86e72)

***

### Далее добавляем изображение в формализацию

```
\scnrelfrom{пример}{\scnfileimage[35em]{путь к файлу изображения}}
```

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/ba684de0-6fc9-43e2-b837-626f18278760)

### [ЧИСЛОem] отвечает за размер изображения

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/2ea78f89-fc83-43f1-afd5-2134c79597de)

***

### Ищем информацию в других источниках

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/fb9efc7e-68f9-4c07-8f8b-bb745030bbdb)

***

### Добавляем в описание

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/ea1abc35-ce86-400e-aa44-1e40413e7145)

***
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/aa820106-b4a3-4bc8-905c-dce5687c8d5a)


***

### Добавим авторов и источники, из которых была взята информация

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/2e6266ad-2121-4945-90ec-fd8093948488)


***




### Следующее понятие - "Облачные вычисления"

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/466b334b-8c06-41ff-b82a-2bdd2c8c5d69)

***

### Описываем для него синонимы и разбиения, относящиеся к этому понятию:

### Для описания характеристик понятия, основных преимуществ, недостатков и т.д. нужно использовать:

```
\begin{scnrelfromlist}{основные характеристики}
    \scnfileitem{}
\end{scnrelfromlist}
```
***
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/9e7716fd-4fe2-4a3d-8ef5-42605136707b)

***
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/4c9a0ae5-dc8c-4825-9f65-3ef3bc64a370)
***
![image](https://github.com/iis-32170x/RPIIS/assets/147609793/72c32b2f-c9d2-460b-a334-26aff42796eb)
***

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/213669f0-a61f-4edb-b90c-31f02b046f30)

***
### Добавляем авторов и источники:

![image](https://github.com/iis-32170x/RPIIS/assets/147609793/bf3f58bb-78f1-4153-9d02-3b7cc2a9f57b)

***

