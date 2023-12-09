---
# Front matter
title: "Отчет по лабораторной работе №7"
subtitle: "Дискретное логарифмирование в конечном поле"
author: "Бурдина Ксения Павловна"
institute: Российский университет дружбы народов, Москва, Россия
date: 9 декабря 2023

# Generic otions
lang: ru-RU
toc-title: "Содержание"

# Pdf output format
toc: true # Table of contents
toc_depth: 2
lof: true # List of figures
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
### Fonts
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase,Scale=0.9
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Misc options
indent: true
header-includes:
  - \linepenalty=10 # the penalty added to the badness of each line within a paragraph (no associated penalty node) Increasing the value makes tex try to have fewer lines in the paragraph.
  - \interlinepenalty=0 # value of the penalty (node) added after each line of a paragraph.
  - \hyphenpenalty=50 # the penalty for line breaking at an automatically inserted hyphen
  - \exhyphenpenalty=50 # the penalty for line breaking at an explicit hyphen
  - \binoppenalty=700 # the penalty for breaking a line at a binary operator
  - \relpenalty=500 # the penalty for breaking a line at a relation
  - \clubpenalty=150 # extra penalty for breaking after first line of a paragraph
  - \widowpenalty=150 # extra penalty for breaking before last line of a paragraph
  - \displaywidowpenalty=50 # extra penalty for breaking before last line before a display math
  - \brokenpenalty=100 # extra penalty for page breaking after a hyphenated line
  - \predisplaypenalty=10000 # penalty for breaking before a display
  - \postdisplaypenalty=0 # penalty for breaking after a display
  - \floatingpenalty = 20000 # penalty for splitting an insertion (can only be split footnote in standard LaTeX)
  - \raggedbottom # or \flushbottom
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Целью данной работы является освоение дискретного логарифмирования в конечном поле, которое применяется во многих алгоритмах криптографии с открытым ключом.

# Задание

1. Изучить алгоритм дискретного логарифмирования в конечном поле.
2. Реализовать представленный алгоритм и вычислить логарифм по заданным числам $p, a, b$.

# Теоретическое введение

Задача дискретного логарифмирования, как и задача разложения на множители, применяется во многих алгоритмах криптографии с открытым ключом. Предложенная в 1976 году У. Диффи и М. Хеллманом для установления сеансового ключа, эта задача послужила основой для создания протоколов шифрования и цифровой подписи, доказательств с нулевым разглашением и других криптографических протоколов.

Пусть над некоторым множеством $\Omega$ произвольной природы определены операции сложения "+" и умножения "$\cdot$". Множество $\Omega$ называется *кольцом*, если вполняются следующие условия:

- Сложение коммутативно: $a+b = b+a$ для любых $a,b \in \Omega$;
- Сложение ассоциативно: $(a+b)+c = a+(b+c)$ для любых $a,b,c \in \Omega$;
- Существует нулевой элемент $0\in \Omega$ такой, что $a+0=a$ для любого $a \in \Omega$;
- Для каждого элемента $a\in \Omega$ существует противоположный элемент $-a\in \Omega$, такой, что $(-a)+a=0$;
- Умножение дистрибутивно относительно сложения:
$$a \cdot (b+c) = a \cdot b + a \cdot c, (a+b) \cdot c = a \cdot c + b \cdot c,$$
для любых $a,b,c \in \Omega$.

Если в кольце $\Omega$ умножение коммутативно: $a \cdot b = b \cdot a$ для любых $a, b \in \Omega$, то кольцо называется *коммутативным*.

Если в кольце $\Omega$ умножение ассоциативно: $(a \cdot b) \cdot c = a \cdot (b \cdot c)$ для любых $a, b, c \in \Omega$, то кольцо называется *ассоциативным*.

Если в кольце $\Omega$ существует едининым элемент $e$ такой, что $a \cdot e = e \cdot a = a$ для любого $a \in \Omega$, то кольцо называется кольцом с единицей.

Если в ассоциативном, коммутативном кольце $\Omega$ с единицей для каждого ненулевого элемента $a$ существует обратный элемент $a^{-1} \in \Omega$ такой, что $a^{-1} \cdot a = a \cdot a^{-1} = e$, то кольцо называется *полем*.

Пусть $m \in N, m > 1$. Целые числа $a$ и $b$ называются *сравнимыми по модулю m* (обозначается $a \equiv b$ ($mod$ $m$)), если разность $a-b$ делится на $m$. Некоторые свойства отношения сравнимости:

- *Рефлексивность*: $a \equiv a$ ($mod$ $m$).
- *Симметричность*: если $a \equiv b$ ($mod$ $m$), то $b \equiv a$ ($mod$ $m$).
- *Транзитивность*: если $a \equiv b$ ($mod$ $m$) и $b \equiv c$ ($mod$ $m$), то $a \equiv c$ ($mod$ $m$).

Отношение, обладающее свойством рефлесивности, симметриности и транзитивности, называется *отношением эквивалентности*. Отношение сравнимости является отношением эквивалентности на множестве $Z$ целых чисел [[2]](https://esystem.rudn.ru/pluginfile.php/2089897/mod_folder/content/0/mathsec_lection12-message-integrity-authentication.pdf?forcedownload=1).

Отношение эквивалентности разбивает множество, на котором оно определено, на *классы эквивалентности*. Любые два класса эквивалентности либо не пересекаются, либо совпадают.

Классы эквивалентности, определяемые отношением сравнимости, называются *классами вычетов по модулю m*. Класс вычетов, содержащий число $a$, обознаается $a$ ($mod$ $m$) или $\bar{a}$ и представляет собой множество чисел вида $a+km$, где $k \in Z$; число $a$ называется представителем этого класса вычетов.

Множество классов вычетов по модулю $m$ обозначается $Z/mZ$, состоит ровно из $m$ элементов и относительно операций сложения и умножения является *кольцом классов вычетов* по модулю $m$.

**Пример**. Если $m=2$, то $Z/2Z=\{0(mod 2), 1(mod 2)\}$, где $0(mod 2) = 2Z$ - множество всех четных чисел, $1(mod 2) = 2Z+1$ - множество всех нечетных чисел.

Обозначим $F_p = Z/pZ$, $p$ - простое целое число и назовем конечным полем из $p$ элементов. Задача дискретного логарифмирования в конечном поле $F_p$ формулируется так: для данных целых чисел $a$ и $b$, $a>1$, $b>p$, найти логарифм - такое целое число $x$, что $a^x \equiv b$ ($mod$ $p$) (если такое число существует). По аналогии с вещественными числами используется обозначение $x = log_a b$.

Безопастность соответствующих криптосистем основана на том, что, зная числа $a,x,p$ вычислить $a^x$ ($mod$ $p$) легко, а решить задачу дискретного логарифмирования трудно. Рассмотрим р-Метод Полларда, который можно применить и для задач дискретного логарифмирования. При этом случайное отображение $f$ должно обладать не только сжимающими свойствами, но и вычислимостью логарифма (логарифм числа $f(c)$ можно выразить через неизвестный логарифм $x$ и $log_a f(c)$). Для дискретного логарифмирования в качестве случайного отображения $f$ чаще всего используются ветвящиеся отображения, например:

$$
f(c)=\begin{cases}
ac,&\text{при $c<\frac p 2$}\\
bc,&\text{при $c>\frac p 2$}
\end{cases}
$$

При $c<\frac p 2$ имеем $log_a f(c) = log_a c+1$, при $c>\frac p 2$ имеем $log_a f(c) = log_a c+x$.

## Алгоритм, реализующий р-метод Полларда для задач дискретного логарифмирования.

*Вход*. Простое число $p$, число $a$ порядка $r$ по модулю $p$, целое число $b, 1<b<p$; отображение $f$, обладающее сжимающими свойствами и сохраняющее вычислимость логарифма.

*Выход*. Показатель $x$, для которого $a^x \equiv b$ ($mod$ $p$), если такой показатель существует.

- выбрать произвольные целые числа $u, v$ и положить $c \leftarrow a^u b^v$ ($mod$ $p$), $d \leftarrow c$
- выполнять $c \leftarrow f(c)(mod p)$, $d \leftarrow f(f(d))(mod p)$, вычисляя при этом логарифмы для $c$ и $d$ как линейные функции от $x$ по модулю $r$, до получения равенства $c \equiv d$ ($mod$ $p$)
- приравняв логарифмы для $c$ и $d$, вычислить логарифм $x$ решением сравнения по модулю $r$. Результат: $x$ или "Решений нет".

**Пример** [[1]](https://intuit.ru/studies/courses/552/408/lecture/9350). Решим задачу дискретного логарифмирования $10^x \equiv 64$ ($mod$ $107$), используя р-Метод Полларда. Порядок числа $10$ по модулю $107$ равен $53$.

Выберем отображение $f(c) = 10c$ ($mod$ $107$) при $c<53$, $f(c) = 64c$ ($mod$ $107$) при $c \geq 53$. Пусть $u=2, v=2$. Результаты вычислений запишем в таблицу:

![Схема работы алгоритма](screens/5.jpg){width=80%}

Приравниваем логарифмы, полученные на 11-м шаге: $7+8x \equiv 13+13x$ ($mod$ $53$). Решая сравнение первой степени, получаем: $x=20$ ($mod$ $53$).

Проверка: $10^{20} \equiv 64$ ($mod$ $107$). 

# Ход выполнения лабораторной работы

Для реализации рассмотренного алгоритма разложения чисел на множители будем использовать среду JupyterLab. Выполним необходимую задачу.

1. Пропишем алгоритм Евклида, который был показан в предыдущих лабораторных работах, а также запишем функцию для вывода его инверсивного значения:

![Расширенный алгоритм Евклида](screens/1.jpg){width=80%}

2. Также пропишем функцию для подсчета значений при выполнении алгоритма Полларда:

![Вспомогательная функция](screens/2.jpg){width=80%}

3. Запишем алгоритм, реализующий *р-метод Полларда*, с помощью следующей функции:

![р-Метод Полларда](screens/3.jpg){width=80%}

Здесь на вход подается простое число $p$, число $a$ порядка $r$ по модулю $p$, целое число $b, 1<b<p$; отображение $f$, обладающее сжимающими свойствами и сохраняющее вычислимость логарифма. Необходимо выполнить следующее:

- выбрать произвольные целые числа $u, v$ и положить $c \leftarrow a^u b^v$ ($mod$ $p$), $d \leftarrow c$
- выполнять $c \leftarrow f(c)(mod p)$, $d \leftarrow f(f(d))(mod p)$, вычисляя при этом логарифмы для $c$ и $d$ как линейные функции от $x$ по модулю $r$, до получения равенства $c \equiv d$ ($mod$ $p$)
- приравняв логарифмы для $c$ и $d$, вычислить логарифм $x$ решением сравнения по модулю $r$. Результат: $x$ или "Решений нет".

По итогу при вызове функции мы получим показатель $x$, для которого $a^x \equiv b$ ($mod$ $p$), если такой показатель существует.

4. Проверим корректность работы алгоритма для заданных сведений. Для этого запишем условие примера с помощью следующей функции:

![Пример работы алгоритма](screens/4.jpg){width=80%}

При вызове данной функции видим, что получаем то же число, что было описано в примере. То есть $x = 20$ ($mod$ $53$) для задачи дискретного логарифмирования $10^x \equiv 64$ ($mod$ $107$).

# Листинг программы

    def alg_e_ext(a, b):
      if b == 0:
        return a, 1, 0
      else:
        d, x, y = alg_e_ext(b, a % b)
      return d, y, x - (a // b) * y
    
    def inv(a, n):
      return alg_e_ext(a, n)[1]

    def fun(x, a, b, xxx):
      (G, H, P, Q) = xxx
      sub = x % 3
      if sub == 0:
        x = x * xxx[0] % xxx[2]
        a = (a + 1) % Q
      if sub == 1:
        x = x * xxx[1] % xxx[2]
        b = (b + 1) % xxx[2]
      if sub == 2:
        x = x * x % xxx[2]
        a = a * 2 % xxx[3]
        b = b * 2 % xxx[3]
      return x, a, b
    
    def Pollard(G, H, P):
      Q = int((P - 1) // 2)
      x = G * H
      a = 1
      b = 1
      X = x
      A = a
      B = b
      for i in range(1, P):
        x, a, b = fun(x, a, b, (G, H, P, Q))
        X, A, B = fun(X, A, B, (G, H, P, Q))
        X, A, B = fun(X, A, B, (G, H, P, Q))
        if x == X:
          break
      nom = a - A
      denom = B - b
      res = (inv(denom, Q) * nom) % Q
      if prov(G, H, P, res):
        return res
      return res + Q
    
    def prov(g, h, p, x):
      return pow(g, x, p) == h
    args = [(10, 64, 107)]
    for arg in args:
      res = Pollard(*arg)
      print(arg, ' : ', res)
      print("Validates: ", prov(arg[0], arg[1], arg[2], res))

# Выводы

В ходе работы мы изучили и реализовали дискретное логарифмирование в конечном поле.

# Список литературы

1. Фороузан Б. А. Криптография и безопасность сетей. - М.: Интернет-Университет Информационных Технологий : БИНОМ. Лаборатория знаний, 2010. - 784 с. [[1]](https://intuit.ru/studies/courses/552/408/lecture/9350)

2. Методические материалы курса [[2]](https://esystem.rudn.ru/pluginfile.php/2089897/mod_folder/content/0/mathsec_lection12-message-integrity-authentication.pdf?forcedownload=1)
