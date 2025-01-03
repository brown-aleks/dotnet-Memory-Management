<head>
  <link rel="stylesheet" type="text/css" href="../styles.css">
</head>

<h2>Термины, связанные с памятью</h2>

<ul>
  <li><a href="#Static-Allocation">Статическое распределение</a></li>
  <li><a href="#The-Register-Machine">Регистровая машина</a></li>
  <li><a href="#The-Stack">Стек</a></li>
  <li><a href="#The-Stack-Machine">Стек-машина</a></li>
  <li><a href="#The-Pointer">Указатель</a></li>
  <li><a href="#The-Heap">Куча</a></li>
</ul>

---

<p class="left-style">Прежде чем начать, полезно рассмотреть некоторые очень важные определения, без которых трудно представить обсуждение темы памяти:</p>
<ul>
    <li><p class="left-style"><i><b>Бит</b></i>: Это наименьшая единица информации, используемая в компьютерных технологиях. Она представляет два возможных состояния, обычно означающих числовые значения 0 и 1 или логические значения true и false. Мы кратко упоминаем, как современные компьютеры хранят отдельные биты в Главе 2. Для представления больших числовых значений необходимо использовать комбинацию нескольких бит для кодирования их в виде двоичного числа, что объясняется следующим образом. При указании размера данных биты указываются строчной буквой b.</p></li>
    <li><p class="left-style"><i><b>Двоичное число</b></i>: это целое числовое значение, представленное в виде последовательности битов. Каждый последующий бит определяет вклад последовательной степени 2 в сумму данного значения. Например, для представления числа 5 используются три последовательных бита со значениями 1, 0 и 1, поскольку 1x1 + 0x2 + 1x4 равно 5. Двоичное число длиной n бит может представлять максимальное значение 2^n – 1. Также часто существует дополнительный бит, выделенный для представления знака значения для кодирования как положительных, так и отрицательных чисел. Существуют также другие, более сложные способы кодирования числовых значений в двоичной форме, особенно для чисел с плавающей точкой.</p></li>
    <li><p class="left-style"><i><b>Двоичный код</b></i>: Вместо числовых значений последовательность битов может представлять определенный набор различных данных, например, символов текста. Каждая последовательность битов назначается определенным данным. Самым базовым и самым популярным на протяжении многих лет был код ASCII, который использует 7-битный двоичный код для представления текста и других символов. Существуют и другие важные двоичные коды, такие как коды операций, кодирующие инструкции, сообщающие компьютеру, что он должен делать.</p></li>
    <li><p class="left-style"><i><b>Байт</i></b>: Исторически это была последовательность битов для кодирования одного символа текста с использованием указанного двоичного кода. Наиболее распространенный размер байта составляет 8 бит, хотя он зависит от архитектуры компьютера и может варьироваться в зависимости от модели. Из-за этой неоднозначности существует более точный термин октет, который означает именно 8-битную единицу данных. Тем не менее, фактическим стандартом является понимание байта как 8-битного значения длины, и как таковой он стал неоспоримым стандартом для определения размеров данных. В настоящее время вряд ли он встретит что-то иное, чем стандартная архитектура с 8-битными байтами. При указании размера данных байты указываются с заглавной буквой B.</p></li>
</ul>

<p class="left-style">Указывая размер данных, мы используем наиболее распространенные кратные (префиксы), определяющие их порядок величины. Это является причиной постоянной путаницы и недопонимания. Такие чрезвычайно популярные термины, как кило, мега и гига, означают умножение тысяч. Один кило равен 1000 (и мы обозначаем его строчной буквой k), один мега равен 1 миллиону (заглавной буквой M) и так далее. С другой стороны, иногда популярным подходом является выражение порядков величины в последовательных умножениях 1024. В таких случаях мы говорим об одном киби, что равно 1024 (обозначается как Ki), один меби равен 1024*1024 (обозначается как Mi), один гиби (Gi) равен 1024*1024*1024 и так далее. Это вносит общую двусмысленность. Когда кто-то говорит об 1 «гигабайте», он может думать о 1 миллиарде байт (1 ГБ) или 1024^3 байт (1 ГиБ) в зависимости от контекста. На практике очень немногие заботятся о точном использовании этих префиксов. В настоящее время принято указывать размер модулей памяти в компьютерах как гигабайты (ГБ), когда они на самом деле являются гибибайтами (ГиБ) или наоборот в случае с жесткими дисками. Даже стандарт JEDEC 100B.01 «Термины, определения и буквенные обозначения для микрокомпьютеров, микропроцессоров и интегральных схем памяти» ссылается на общее использование K, M и G как умножений 1024, не осуждая его явно. В таких ситуациях нам остается только полагаться на здравый смысл в понимании этих префиксов из контекста.</p>

<p class="left-style">В настоящее время мы очень привыкли к таким терминам, как ОЗУ или постоянное хранилище, установленное в наших компьютерах. Даже умные часы теперь оснащены 32 ГиБ ОЗУ! Вы можете легко забыть, что первые компьютеры не были оснащены такой роскошью. Можно сказать, что они не были оснащены ничем. На <a href="#f-1-1">рисунке 1-1</a> показаны различные элементы компьютера:</p>

<ul>
    <li><p class="left-style"><i><b>Память</b>: отвечает за хранение данных и самой программы. Способ реализации памяти со временем значительно изменился, начиная с вышеупомянутых перфокарт, через магнитные типы и электронно-лучевые трубки, до используемых в настоящее время транзисторов. Память можно разделить на две основные подкатегории:</i></p></li>
    <ul>
      <li><p class="left-style"><i><b>Оперативная память (ОЗУ)</b>: позволяет считывать данные в одно и то же время доступа независимо от области памяти, к которой осуществляется доступ. На практике, как вы увидите в Главе 2, современная память удовлетворяет этому условию лишь приблизительно по технологическим причинам.</i></p></li>
      <li><p class="left-style"><i><b>Неравномерная память доступа</b>: в отличие от ОЗУ, время, необходимое для доступа к памяти, зависит от ее расположения на физическом носителе. Это, очевидно, включает в себя перфокарты, магнитные типы, классические жесткие диски, CD и DVD и т. д., где носители информации должны быть позиционированы (например, повернуты) в правильное положение перед доступом.</i></p></li>
    </ul>
    <li><p class="left-style"><i><b>Адрес</b>: представляет собой определенное местоположение во всей области памяти. Обычно выражается в байтах, поскольку один байт — это наименьшая возможная адресуемая гранулярность на многих платформах.</i></p></li>
    <li><p class="left-style"><i><b>Арифметико-логическое устройство (АЛУ)</b>: отвечает за выполнение таких операций, как сложение и вычитание. Это ядро ​​компьютера, где выполняется большая часть работы. В настоящее время компьютеры включают в себя более одного АЛУ, что позволяет распараллеливать вычисления.</i></p></li>
    <li><p class="left-style"><i><b>Блок управления</b>: декодирует инструкции программы (коды операций), считанные из памяти. На основе описания внутренней инструкции он знает, какую арифметическую или логическую операцию следует выполнить и над какими данными.</i></p></li>
    <li><p class="left-style"><i><b>Регистр</b>: ячейка памяти, быстро доступная из АЛУ и/или блока управления (которые мы можем вместе называть исполнительными блоками), обычно содержащаяся в ней. Упомянутые выше аккумуляторы представляют собой специальный, упрощенный вид регистров. Регистры чрезвычайно быстры с точки зрения времени доступа, и фактически нет данных более близких к исполнительным блокам.</i></p></li>
    <li><p class="left-style"><i><b>Слово</b>: Базовая единица данных фиксированного размера, используемая в конкретном компьютерном проектировании. Она отражена во многих областях проектирования, таких как размер большинства регистров, максимальный адрес или самый большой блок данных, передаваемый за одну операцию. Чаще всего она выражается в количестве бит (называемых размером слова или длиной слова). Большинство современных компьютеров являются 32- или 64-разрядными, поэтому они имеют длину слова 32 и 64 бита соответственно, регистры длиной 32 или 64 бита и т. д.</i></p></li>
</ul>

<p class="left-style">Блок управления использует дополнительный регистр, называемый указателем инструкций (IP) или счетчиком программ (PC), для указания на текущую выполняемую инструкцию. Обычное выполнение программы так же просто, как увеличение адреса, хранящегося в PC, на следующие инструкции. Такие вещи, как циклы или переходы, так же просты, как изменение значения указателя инструкций на следующую инструкцию для выполнения, обозначая, какую инструкцию мы хотим, чтобы программа продолжила выполнять.</p>

<br/>

![Рисунок 1.1](../Images/figures/1-1.png)

<p class="left-style" id="f-1-1"><i><b>Рисунок 1-1.</b> Компьютерная диаграмма с хранимой программой – память + указатель инструкций</i></p>

<p class="left-style">Первые компьютеры программировались с использованием двоичного кода, который напрямую описывал инструкции для выполнения. Однако с ростом сложности программ это решение становилось все более обременительным. Был разработан новый язык программирования (обозначаемый как языки программирования второго поколения – 2GL) для описания кода более доступным способом с помощью так называемого ассемблерного кода. Это текстовое и очень краткое описание отдельных инструкций, выполняемых процессором. Это было намного удобнее, чем прямое двоичное кодирование. Затем были разработаны даже языки более высокого уровня (3GL), такие как известные C, C++ или Pascal.</p>

<p class="left-style">Для нас интересно то, что все эти языки должны быть преобразованы из текстовой в двоичную форму, а затем помещены в память компьютера. Это преобразование называется компиляцией, а инструмент, который его запускает, называется компилятором. В случае ассемблерного кода мы скорее называем его сборкой с помощью инструмента ассемблера. В конечном итоге результатом является программа в формате двоичного кода, которая может быть позже выполнена – последовательность кодов операций и их аргументов (операндов).</p>

<p class="left-style">Вооружившись этими базовыми знаниями, вы теперь можете начать свой путь в управлении памятью!</p>

### <h3 id="Static-Allocation">Статическое распределение</h3>

<p class="left-style">Большинство самых первых языков программирования допускали только статическое выделение памяти — объем и точное местоположение необходимой памяти должны были быть известны во время компиляции, еще до выполнения программы. С фиксированными и предопределенными размерами управление памятью было тривиальным. Все основные языки программирования «древних времен», начиная с машинного или ассемблерного кода и заканчивая первыми версиями FORTRAN и ALGOL, имели такие ограниченные возможности. Но у них также есть много недостатков. Статическое выделение памяти может легко привести к неэффективному использованию памяти. Не зная заранее, какой объем данных будет обработан, как мы вычислим, сколько памяти нам следует выделить? Это делает программы ограниченными и негибкими. В общем, такую ​​программу нужно снова компилировать для обработки больших объемов данных.</p>

<p class="left-style">В самых первых компьютерах все выделения были статическими, поскольку используемые ячейки памяти (аккумулятор, регистры или ячейки оперативной памяти) определялись во время кодирования программы. Поэтому определенные «переменные» существовали в течение всего жизненного цикла программы. В настоящее время мы все еще используем статическое распределение в том смысле, что при создании статических глобальных переменных и тому подобных, хранящихся в специальном сегменте данных программы. В последующих главах вы увидите, где они хранятся в случае программ .NET.</p>

### <h3 id="The-Register-Machine">Регистровая машина</h3>

<p class="left-style">Компьютеры используют регистры (или аккумуляторы как частный случай) для работы с арифметико-логическими устройствами (АЛУ). Машины, которые полагаются на эту конструкцию, называются регистровыми машинами. Это потому, что при выполнении программ на таком компьютере мы фактически производим вычисления в регистрах. Если мы хотим сложить, разделить или сделать что-то еще, мы должны загрузить соответствующие данные из памяти в соответствующие регистры. Затем мы вызываем определенную инструкцию, чтобы вызвать соответствующую операцию над ними, а затем еще одну, чтобы сохранить результат из одного из регистров обратно в память.</p>

<p class="left-style">Предположим, мы хотим написать программу, которая вычисляет выражение s=x+(2*y)+z на компьютере с двумя регистрами — A и B. Предположим также, что</p>

<ul>
    <li><p class="left-style">s, x, y и z — адреса памяти, в которых хранятся некоторые значения.</p></li>
    <li><p class="left-style">Некоторый низкоуровневый псевдоассемблерный код с такими инструкциями, как «Загрузить», «Сложить» и «Умножить».</p></li>
</ul>

<p class="left-style">Такую теоретическую машину можно запрограммировать с помощью простой программы, представленной в <a href="#l-1-1">листинге 1-1</a>.</p>

<br/>

<p class="left-style" id="l-1-1"><i><b>Листинг 1-1.</b> Псевдокод примера программы, реализующей вычисление s=x+(2*y)+z на простой двух-регистровой машине. Комментарии показывают состояние регистра после выполнения каждой инструкции</i></p>

```asm
Load      A, y        // A = y
Multiply  A, 2        // A = A * 2 = 2 * y
Load      B, x        // B = x
Add       A, B        // A = A + B = x + 2 * y
Load      B, z        // B = z
Add       A, B        // A = A + B = x + 2 * y + z
Store     s, A        // s = A
```

<p class="left-style">Если этот код напоминает вам x86 или любой другой ассемблерный код, который вы когда-либо изучали, это не совпадение! Это потому, что большинство современных компьютеров — это сложные регистровые машины. Все процессоры Intel и AMD, которые мы используем в наших компьютерах, работают таким образом. При написании ассемблерного кода на базе x86/x64 мы оперируем регистрами общего назначения, такими как eax, ebx, ecx и т. д. Конечно, есть еще много инструкций, других специализированных регистров и т. д. Но концепция та же самая.</p>

> [!NOTE] Примечание  
>Можете ли вы представить себе машину с набором инструкций, которая позволяет выполнять операцию непосредственно в памяти без необходимости загрузки данных в регистры? Следуя псевдоассемблеру, это может выглядеть гораздо более лаконично и высокоуровнево, поскольку больше нет необходимости в инструкциях загрузки/сохранения:
>```asm  
>Multiply        s, y, 2      // s = 2 * y
>Add             s, x         // s = s + x = 2 * y + x
>Add             s, z         // s = s + z = 2 * y + x + z
>```
>Да, были такие машины, как IBM system/360, но на сегодняшний день нам не известно ни об одном серийно используемом компьютере такого типа.

### <h3 id="The-Stack">Стек</h3>

<p class="left-style">Концептуально стек представляет собой структуру данных, которую можно просто описать как список «последним пришел, первым ушел» (LIFO). Он
позволяет выполнять две основные операции: добавлять некоторые данные наверх («push») и удалять + возвращать некоторые данные сверху («pop»), как показано на <a href="#f-1-2">рисунке 1-2</a>.</p>

<br/>

<p class="left-style" id="f-1-2"><i><b>Рисунок 1-2.</b> Операции извлечения и добавления стека. Это только концептуальный рисунок, не связанный с какой-либо конкретной моделью памяти и реализацией</i></p>

![Рисунок 1.2](../Images/figures/1-2.png)

<br/>

<p class="left-style">Концепция стека стала неотъемлемо связана с программированием с самого начала, в основном из-за концепции подпрограммы. Сегодняшний .NET активно использует концепции «стек вызовов» и «стек», поэтому давайте посмотрим, как все начиналось. Первоначальное значение стека как структуры данных по-прежнему актуально (например, в .NET есть коллекция Stack&ltT&gt).</p>

<p class="left-style">Стек является очень важным аспектом управления памятью, поскольку при программировании в .NET туда может быть помещено много наших данных. Давайте подробнее рассмотрим стек и его использование в вызовах функций. Мы будем использовать пример программы из <a href="#l-1-2">листинга 1-2</a>, написанный на псевдокоде в стиле C, который вызывает две функции – main вызывает fun1 (передавая два аргумента a и b), которая имеет две локальные переменные x и y. Затем функция fun1 в какой-то момент вызывает функцию fun2 (передавая один аргумент n), которая имеет одну локальную переменную z.</p>

<br/>

<p class="left-style" id="l-1-2"><i><b>Листинг 1-2.</b> Псевдокод программы, вызывающей функцию внутри другой функции</i></p>

```cpp
void main()
{
	...
	fun1(2, 3);
	...
}

int fun1(int a, int b)
{
	int x, y;
	...
	fun2(a+b);
}

int fun2(int n)
{
	int z;
	...
}
```

<p class="left-style">Сначала представьте себе непрерывную область памяти, предназначенную для обработки стека, нарисованную таким образом, что последующие ячейки памяти имеют растущие адреса (см. левую часть <a href="#f-1-3a">рисунка 1-3a</a>), и вторую область памяти, где находится ваш программный код (см. правую часть <a href="#f-1-3a">рисунка 1-3a</a>), организованную таким же образом. Поскольку код функций не обязательно должен лежать рядом друг с другом, блоки кода main, fun1 и fun2 нарисованы раздельно. Выполнение программы из <a href="#l-1-2">листинга 1-2</a> можно описать следующими шагами:</p>

<br/>

<ol>
  <li>
    <p class="left-style">Прямо перед вызовом fun1 внутри main (см. рисунок 1-3a). Очевидно, что поскольку программа уже запущена, некоторая область стека уже создана (серая часть в верхней части области стека на рисунке 1-3a). Указатель стека (SP) хранит адрес, указывающий текущую границу стека. Счетчик программ (PC) указывает куда-то внутри функции main (мы обозначили это как адрес A1), прямо перед инструкцией вызвать fun1.</p>
    <br/>
    <p class="left-style" id="f-1-3a"><i><b>Рисунок 1-3a.</b> Стек и области памяти кода – в момент перед вызовом функции fun1 из <a href="#l-1-2">листинга 1-2</a></i></p>
    <img src="../Images/figures/1-3a.png" alt="Рисунок 1.3a">

  ![Рисунок 1.3a](/Images/figures/1-3a.png)

  </li>

  <br/>

  <li><p class="left-style">После вызова fun1 внутри main (см. <a href="#f-1-3b">рис. 1-3b</a>). При вызове функции стек расширяется путем перемещения SP для хранения необходимой информации. Это дополнительное пространство включает</p>
  <ul>
    <li><p class="left-style"><i><b>Аргументы</b></i>: Все аргументы функции могут быть сохранены в стеке. В нашем примере там были сохранены значения аргументов a (2) и b (3).</p></li>
    <li><p class="left-style"><i><b>Return address</b></i>: To have a possibility to continue main function execution after executing fun1, the next instruction’s address just after the function call is saved on stack. In our case, we denoted it as the A1+1 address (pointing to the next instruction after instruction under the A1 address).</p></li>
  </ul></li>
</ol>


### <h3 id="The-Stack-Machine">Стек машина</h3>



<p class="left-style"></p>
<p class="left-style"></p>

### <h3 id="The-Pointer">Указатель</h3>

<p class="left-style"></p>
<p class="left-style"></p>
<p class="left-style"></p>
<p class="left-style"></p>

### <h3 id="The-Heap">Куча</h3>

<p class="left-style"></p>
<p class="left-style"></p>
<p class="left-style"></p>
<p class="left-style"></p>
