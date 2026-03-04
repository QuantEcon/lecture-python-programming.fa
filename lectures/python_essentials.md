---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
heading-map:
  overview: مرور کلی
  data-types: انواع داده
  primitive-data-types: انواع داده ابتدایی
  boolean-values: مقادیر Boolean
  numeric-types: انواع عددی
  containers: ظروف
  slice-notation: نشانه‌گذاری Slice
  sets-and-dictionaries: Set‌ها و Dictionary‌ها
  input-and-output: ورودی و خروجی
  paths: مسیرها
  iterating: تکرار
  looping-over-different-objects: حلقه زدن روی اشیاء مختلف
  looping-without-indices: حلقه زدن بدون ایندکس‌ها
  list-comprehensions: List Comprehension‌ها
  comparisons-and-logical-operators: مقایسه‌ها و عملگرهای منطقی
  comparisons: مقایسه‌ها
  combining-expressions: ترکیب عبارات
  coding-style-and-documentation: سبک کدنویسی و مستندسازی
  python-style-guidelines-pep8: 'راهنمای سبک Python: PEP8'
  docstrings: Docstring‌ها
  exercises: تمرین‌ها
---

(python_done_right)=
```{raw} jupyter
<div id="qe-notebook-header" align="right" style="text-align:right;">
        <a href="https://quantecon.org/" title="quantecon.org">
                <img style="width:250px;display:inline;" width="250px" src="https://assets.quantecon.org/img/qe-menubar-logo.svg" alt="QuantEcon">
        </a>
</div>
```

# اصول Python

## مرور کلی

ما حجم زیادی از مطالب را به سرعت پوشش داده‌ایم، با تمرکز بر مثال‌ها.

اکنون بیایید برخی از ویژگی‌های اصلی Python را به شیوه‌ای منظم‌تر بررسی کنیم.

این رویکرد هیجان‌انگیزتر نیست اما به روشن شدن برخی جزئیات کمک می‌کند.

## انواع داده

```{index} single: Python; Data Types
```

برنامه‌های کامپیوتری معمولاً طیف وسیعی از انواع داده را پیگیری می‌کنند.

برای مثال، `1.5` یک عدد اعشاری است، در حالی که `1` یک عدد صحیح است.

برنامه‌ها به دلایل مختلف نیاز به تمایز بین این دو نوع دارند.

یکی از دلایل این است که آن‌ها به صورت متفاوتی در حافظه ذخیره می‌شوند.

دلیل دیگر این است که عملیات حسابی متفاوت هستند

* به عنوان مثال، محاسبات اعشاری در بیشتر ماشین‌ها توسط یک واحد اعشاری تخصصی (FPU) پیاده‌سازی می‌شود.

به طور کلی، اعداد اعشاری اطلاعات بیشتری دارند اما عملیات حسابی روی اعداد صحیح سریع‌تر و دقیق‌تر است.

Python انواع داده داخلی متعددی را فراهم می‌کند که برخی از آن‌ها را قبلاً دیده‌ایم

* رشته‌ها، لیست‌ها، و غیره.

بیایید کمی بیشتر درباره آن‌ها یاد بگیریم.

### انواع داده ابتدایی

(boolean)=
#### مقادیر Boolean

یکی از انواع داده ساده، مقادیر **Boolean** است که می‌تواند `True` یا `False` باشد

```{code-cell} python3
x = True
x
```

می‌توانیم نوع هر شیء در حافظه را با استفاده از تابع `type()` بررسی کنیم.

```{code-cell} python3
type(x)
```

در خط بعدی کد، مفسر عبارت سمت راست = را ارزیابی می‌کند و y را به این مقدار متصل می‌کند

```{code-cell} python3
y = 100 < 10
y
```

```{code-cell} python3
type(y)
```

در عبارات حسابی، `True` به `1` و `False` به `0` تبدیل می‌شود.

این به آن **محاسبات Boolean** گفته می‌شود و اغلب در برنامه‌نویسی مفید است.

در اینجا چند مثال آورده شده است

```{code-cell} python3
x + y
```

```{code-cell} python3
x * y
```

```{code-cell} python3
True + True
```

```{code-cell} python3
bools = [True, True, False, True]  # List of Boolean values

sum(bools)
```

#### انواع عددی

انواع عددی نیز انواع داده ابتدایی مهمی هستند.

ما قبلاً انواع `integer` و `float` را دیده‌ایم.

**اعداد مختلط** یکی دیگر از انواع داده ابتدایی در Python هستند

```{code-cell} python3
x = complex(1, 2)
y = complex(2, 1)
print(x * y)

type(x)
```

### ظروف

Python چندین نوع اساسی برای ذخیره مجموعه‌هایی از داده‌های (احتمالاً ناهمگن) دارد.

ما قبلاً {ref}`لیست‌ها را بحث کرده‌ایم <lists_ref>`.

```{index} single: Python; Tuples
```

یک نوع داده مرتبط، **tuple** است که لیست‌های "تغییرناپذیر" هستند

```{code-cell} python3
x = ('a', 'b')  # Parentheses instead of the square brackets
x = 'a', 'b'    # Or no brackets --- the meaning is identical
x
```

```{code-cell} python3
type(x)
```

در Python، یک شیء **immutable** نامیده می‌شود اگر پس از ایجاد، نتوان آن را تغییر داد.

برعکس، یک شیء **mutable** است اگر پس از ایجاد بتوان آن را تغییر داد.

لیست‌های Python قابل تغییر هستند

```{code-cell} python3
x = [1, 2]
x[0] = 10
x
```

اما tuple‌ها قابل تغییر نیستند

```{code-cell} python3
---
tags: [raises-exception]
---
x = (1, 2)
x[0] = 10
```

کمی بعدتر درباره نقش داده‌های قابل تغییر و تغییرناپذیر بیشتر صحبت خواهیم کرد.

tuple‌ها (و لیست‌ها) را می‌توان به صورت زیر "باز کرد"

```{code-cell} python3
integers = (10, 20, 30)
x, y, z = integers
x
```

```{code-cell} python3
y
```

در واقع شما قبلاً {ref}`نمونه‌ای از این را دیده‌اید <tuple_unpacking_example>`.

باز کردن tuple راحت است و ما اغلب از آن استفاده خواهیم کرد.

#### نشانه‌گذاری Slice

```{index} single: Python; Slicing
```

برای دسترسی به چندین عنصر از یک دنباله (یک لیست، یک tuple یا یک رشته)، می‌توانید از نشانه‌گذاری slice در Python استفاده کنید.

برای مثال،

```{code-cell} python3
a = ["a", "b", "c", "d", "e"]
a[1:]
```

```{code-cell} python3
a[1:3]
```

قانون کلی این است که `a[m:n]` تعداد `n - m` عنصر را بازمی‌گرداند، که از `a[m]` شروع می‌شود.

اعداد منفی نیز مجاز هستند

```{code-cell} python3
a[-2:]  # Last two elements of the list
```

همچنین می‌توانید از فرمت `[start:end:step]` برای مشخص کردن گام استفاده کنید

```{code-cell} python3
a[::2]
```

با استفاده از یک گام منفی، می‌توانید دنباله را به ترتیب معکوس برگردانید

```{code-cell} python3
a[-2::-1] # Walk backwards from the second last element to the first element
```

همان نشانه‌گذاری slice روی tuple‌ها و رشته‌ها کار می‌کند

```{code-cell} python3
s = 'foobar'
s[-3:]  # Select the last three elements
```

#### Set‌ها و Dictionary‌ها

```{index} single: Python; Sets
```

```{index} single: Python; Dictionaries
```

دو نوع ظرف دیگر که باید قبل از ادامه به آن‌ها اشاره کنیم [set‌ها](https://docs.python.org/3/tutorial/datastructures.html#sets) و [dictionary‌ها](https://docs.python.org/3/tutorial/datastructures.html#dictionaries) هستند.

dictionary‌ها بسیار شبیه لیست‌ها هستند، با این تفاوت که آیتم‌ها به جای شماره‌گذاری، نام‌گذاری می‌شوند

```{code-cell} python3
d = {'name': 'Frodo', 'age': 33}
type(d)
```

```{code-cell} python3
d['age']
```

نام‌های `'name'` و `'age'` *کلیدها* نامیده می‌شوند.

اشیایی که کلیدها به آن‌ها نگاشت می‌شوند (`'Frodo'` و `33`) `مقادیر` نامیده می‌شوند.

set‌ها مجموعه‌های بدون ترتیب و بدون تکرار هستند، و متدهای set عملیات معمول نظریه مجموعه‌ها را فراهم می‌کنند

```{code-cell} python3
s1 = {'a', 'b'}
type(s1)
```

```{code-cell} python3
s2 = {'b', 'c'}
s1.issubset(s2)
```

```{code-cell} python3
s1.intersection(s2)
```

تابع `set()` از دنباله‌ها set می‌سازد

```{code-cell} python3
s3 = set(('foo', 'bar', 'foo'))
s3
```

## ورودی و خروجی

```{index} single: Python; IO
```

بیایید به طور خلاصه خواندن و نوشتن در فایل‌های متنی را مرور کنیم، که با نوشتن شروع می‌شود

```{code-cell} python3
f = open('newfile.txt', 'w')   # Open 'newfile.txt' for writing
f.write('Testing\n')           # Here '\n' means new line
f.write('Testing again')
f.close()
```

در اینجا

* تابع داخلی `open()` یک شیء فایل برای نوشتن ایجاد می‌کند.
* هر دو `write()` و `close()` متدهای اشیاء فایل هستند.

این فایلی که ساخته‌ایم کجاست؟

به یاد داشته باشید که Python مفهوم دایرکتوری کاری فعلی (pwd) را حفظ می‌کند که می‌توان آن را از Jupyter یا IPython از طریق زیر یافت

```{code-cell} ipython
%pwd
```

اگر مسیری مشخص نشود، Python در اینجا می‌نویسد.

همچنین می‌توانیم از Python برای خواندن محتویات `newline.txt` به صورت زیر استفاده کنیم

```{code-cell} python3
f = open('newfile.txt', 'r')
out = f.read()
out
```

```{code-cell} python3
print(out)
```

در واقع، رویکرد توصیه شده در Python مدرن استفاده از عبارت `with` است تا اطمینان حاصل شود که فایل‌ها به درستی دریافت و آزاد می‌شوند.

محدود کردن عملیات در همان بلوک نیز وضوح کد شما را بهبود می‌بخشد.

```{note}
این نوع بلوک به طور رسمی به عنوان یک [*context*](https://realpython.com/python-with-statement/#the-with-statement-approach) شناخته می‌شود.
```

بیایید سعی کنیم دو مثال بالا را به عبارت `with` تبدیل کنیم.

ابتدا مثال نوشتن را تغییر می‌دهیم
```{code-cell} python3

with open('newfile.txt', 'w') as f:  
    f.write('Testing\n')         
    f.write('Testing again')
```

توجه کنید که نیازی به فراخوانی متد `close()` نداریم زیرا بلوک `with` اطمینان حاصل می‌کند که جریان در انتهای بلوک بسته می‌شود.

با تغییرات جزئی، می‌توانیم فایل‌ها را با استفاده از `with` نیز بخوانیم

```{code-cell} python3
with open('newfile.txt', 'r') as fo:
    out = fo.read()
    print(out)
```
اکنون فرض کنید که می‌خواهیم ورودی را از یک فایل بخوانیم و خروجی را در فایل دیگری بنویسیم.
در اینجا نحوه انجام این کار در حالی که به درستی منابع را دریافت و به سیستم عامل بازمی‌گردانیم با استفاده از عبارات `with` آمده است:

```{code-cell} python3
with open("newfile.txt", "r") as f:
    file = f.readlines()
    with open("output.txt", "w") as fo:
        for i, line in enumerate(file):
            fo.write(f'Line {i}: {line} \n')
```

فایل خروجی به صورت زیر خواهد بود

```{code-cell} python3
with open('output.txt', 'r') as fo:
    print(fo.read())
```

می‌توانیم مثال بالا را با گروه‌بندی دو عبارت `with` در یک خط ساده کنیم

```{code-cell} python3
with open("newfile.txt", "r") as f, open("output2.txt", "w") as fo:
        for i, line in enumerate(f):
            fo.write(f'Line {i}: {line} \n')
```

فایل خروجی یکسان خواهد بود

```{code-cell} python3
with open('output2.txt', 'r') as fo:
    print(fo.read())
```

فرض کنید می‌خواهیم به نوشتن در فایل موجود ادامه دهیم به جای بازنویسی آن.

می‌توانیم حالت را به `a` تغییر دهیم که مخفف حالت append است

```{code-cell} python3
with open('output2.txt', 'a') as fo:
    fo.write('\nThis is the end of the file')
```

```{code-cell} python3
with open('output2.txt', 'r') as fo:
    print(fo.read())
```

```{note}
توجه کنید که ما فقط حالت‌های `r`، `w` و `a` را در اینجا پوشش دادیم که رایج‌ترین حالت‌ها هستند.
Python [حالت‌های متنوعی](https://www.geeksforgeeks.org/python/reading-writing-text-files-python/) ارائه می‌دهد که می‌توانید با آن‌ها آزمایش کنید.
```

### مسیرها

```{index} single: Python; Paths
```

توجه کنید که اگر `newfile.txt` در دایرکتوری کاری فعلی نباشد، این فراخوانی `open()` شکست می‌خورد.

در این حالت، می‌توانید فایل را به pwd منتقل کنید یا [مسیر کامل](https://en.wikipedia.org/wiki/Path_%28computing%29) فایل را مشخص کنید

```{code-block} python3
:class: no-execute

f = open('insert_full_path_to_file/newfile.txt', 'r')
```

(iterating_version_1)=
## تکرار

```{index} single: Python; Iteration
```

یکی از مهم‌ترین وظایف در محاسبات، گذر از یک دنباله از داده‌ها و انجام یک عمل مشخص است.

یکی از نقاط قوت Python رابط ساده و انعطاف‌پذیر آن برای این نوع تکرار از طریق حلقه `for` است.

### حلقه زدن روی اشیاء مختلف

بسیاری از اشیاء Python "قابل تکرار" هستند، به این معنی که می‌توان روی آن‌ها حلقه زد.

برای ارائه یک مثال، بیایید فایل us_cities.txt را بنویسیم که شهرهای ایالات متحده و جمعیت آن‌ها را فهرست می‌کند، در دایرکتوری کاری فعلی.

(us_cities_data)=
```{code-cell} ipython
%%writefile us_cities.txt
new york: 8244910
los angeles: 3819702
chicago: 2707120
houston: 2145146
philadelphia: 1536471
phoenix: 1469471
san antonio: 1359758
san diego: 1326179
dallas: 1223229
```

در اینجا `%%writefile` یک [IPython cell magic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cell-magics) است.

فرض کنید می‌خواهیم اطلاعات را خواناتر کنیم، با بزرگ کردن نام‌ها و اضافه کردن کاما برای نشان دادن هزارگان.

برنامه زیر داده‌ها را می‌خواند و تبدیل را انجام می‌دهد:

```{code-cell} python3
data_file = open('us_cities.txt', 'r')
for line in data_file:
    city, population = line.split(':')         # Tuple unpacking
    city = city.title()                        # Capitalize city names
    population = f'{int(population):,}'        # Add commas to numbers
    print(city.ljust(15) + population)
data_file.close()
```

در اینجا `f'` یک f-string است [که برای درج متغیرها در رشته‌ها استفاده می‌شود](https://docs.python.org/3/library/string.html#formatspec).

قالب‌بندی مجدد هر خط نتیجه سه متد مختلف رشته است که جزئیات آن‌ها را می‌توان برای بعد گذاشت.

بخش جالب این برنامه برای ما خط 2 است که نشان می‌دهد

1. شیء فایل `data_file` قابل تکرار است، به این معنی که می‌تواند در سمت راست `in` در یک حلقه `for` قرار گیرد.
1. تکرار از هر خط در فایل عبور می‌کند.

این منجر به نحو تمیز و راحتی می‌شود که در برنامه ما نشان داده شده است.

بسیاری از انواع دیگر اشیاء قابل تکرار هستند و ما بعداً درباره برخی از آن‌ها بحث خواهیم کرد.

### حلقه زدن بدون ایندکس‌ها

یکی از چیزهایی که ممکن است متوجه شده باشید این است که Python تمایل دارد حلقه زدن بدون ایندکس‌گذاری صریح را ترجیح دهد.

برای مثال،

```{code-cell} python3
x_values = [1, 2, 3]  # Some iterable x
for x in x_values:
    print(x * x)
```

به این ترجیح داده می‌شود

```{code-cell} python3
for i in range(len(x_values)):
    print(x_values[i] * x_values[i])
```

وقتی این دو گزینه را مقایسه می‌کنید، می‌توانید ببینید چرا اولی ترجیح داده می‌شود.

Python امکاناتی برای ساده‌سازی حلقه زدن بدون ایندکس فراهم می‌کند.

یکی `zip()` است که برای گذر از جفت‌ها از دو دنباله استفاده می‌شود.

برای مثال، کد زیر را اجرا کنید

```{code-cell} python3
countries = ('Japan', 'Korea', 'China')
cities = ('Tokyo', 'Seoul', 'Beijing')
for country, city in zip(countries, cities):
    print(f'The capital of {country} is {city}')
```

تابع `zip()` همچنین برای ایجاد dictionary‌ها مفید است --- برای مثال

```{code-cell} python3
names = ['Tom', 'John']
marks = ['E', 'F']
dict(zip(names, marks))
```

اگر واقعاً به ایندکس از یک لیست نیاز داریم، یکی از گزینه‌ها استفاده از `enumerate()` است.

برای درک اینکه `enumerate()` چه کاری انجام می‌دهد، مثال زیر را در نظر بگیرید

```{code-cell} python3
letter_list = ['a', 'b', 'c']
for index, letter in enumerate(letter_list):
    print(f"letter_list[{index}] = '{letter}'")
```
(list_comprehensions)=
### List Comprehension‌ها

```{index} single: Python; List comprehension
```

همچنین می‌توانیم کد تولید لیست برداشت‌های تصادفی را با استفاده از چیزی به نام *list comprehension* به طور قابل توجهی ساده کنیم.

[List comprehension‌ها](https://en.wikipedia.org/wiki/List_comprehension) یک ابزار زیبای Python برای ایجاد لیست هستند.

مثال زیر را در نظر بگیرید، که list comprehension در سمت راست خط دوم است

```{code-cell} python3
animals = ['dog', 'cat', 'bird']
plurals = [animal + 's' for animal in animals]
plurals
```

این یک مثال دیگر است

```{code-cell} python3
range(8)
```

```{code-cell} python3
doubles = [2 * x for x in range(8)]
doubles
```

## مقایسه‌ها و عملگرهای منطقی

### مقایسه‌ها

```{index} single: Python; Comparison
```

بسیاری از انواع مختلف عبارات به یکی از مقادیر Boolean (یعنی `True` یا `False`) ارزیابی می‌شوند.

یک نوع رایج، مقایسه‌ها هستند، مانند

```{code-cell} python3
x, y = 1, 2
x < y
```

```{code-cell} python3
x > y
```

یکی از ویژگی‌های خوب Python این است که می‌توانیم نامساوی‌ها را *زنجیره* کنیم

```{code-cell} python3
1 < 2 < 3
```

```{code-cell} python3
1 <= 2 <= 3
```

همانطور که قبلاً دیدیم، هنگام آزمایش برابری از `==` استفاده می‌کنیم

```{code-cell} python3
x = 1    # Assignment
x == 2   # Comparison
```

برای "نابرابر" از `!=` استفاده کنید

```{code-cell} python3
1 != 2
```

توجه کنید که هنگام آزمایش شرایط، می‌توانیم از **هر** عبارت معتبر Python استفاده کنیم

```{code-cell} python3
x = 'yes' if 42 else 'no'
x
```

```{code-cell} python3
x = 'yes' if [] else 'no'
x
```

اینجا چه اتفاقی می‌افتد؟

قانون این است:

* عباراتی که به صفر، دنباله‌ها یا ظروف خالی (رشته‌ها، لیست‌ها و غیره) و `None` ارزیابی می‌شوند، همه معادل `False` هستند.
    * به عنوان مثال، `[]` و `()` در یک شرط `if` معادل `False` هستند
* همه مقادیر دیگر معادل `True` هستند.
    * به عنوان مثال، `42` در یک شرط `if` معادل `True` است

### ترکیب عبارات

```{index} single: Python; Logical Expressions
```

می‌توانیم عبارات را با استفاده از `and`، `or` و `not` ترکیب کنیم.

این‌ها ربط‌دهنده‌های منطقی استاندارد (عطف، فصل و نفی) هستند

```{code-cell} python3
1 < 2 and 'f' in 'foo'
```

```{code-cell} python3
1 < 2 and 'g' in 'foo'
```

```{code-cell} python3
1 < 2 or 'g' in 'foo'
```

```{code-cell} python3
not True
```

```{code-cell} python3
not not True
```

به خاطر بسپارید

* `P and Q` زمانی `True` است که هر دو `True` باشند، در غیر این صورت `False`
* `P or Q` زمانی `False` است که هر دو `False` باشند، در غیر این صورت `True`

همچنین می‌توانیم از `all()` و `any()` برای آزمایش دنباله‌ای از عبارات استفاده کنیم

```{code-cell} python3
all([1 <= 2 <= 3, 5 <= 6 <= 7])
```
```{code-cell} python3
all([1 <= 2 <= 3, "a" in "letter"])
```
```{code-cell} python3
any([1 <= 2 <= 3, "a" in "letter"])
```

```{note}
* `all()` زمانی `True` برمی‌گرداند که *همه* مقادیر/عبارات boolean در دنباله `True` باشند
* `any()` زمانی `True` برمی‌گرداند که *هر* مقدار/عبارت boolean در دنباله `True` باشد
```

## سبک کدنویسی و مستندسازی

سبک کدنویسی سازگار و استفاده از مستندسازی می‌تواند کد را راحت‌تر برای درک و نگهداری کند.

### راهنمای سبک Python: PEP8

```{index} single: Python; PEP8
```

می‌توانید فلسفه برنامه‌نویسی Python را با تایپ کردن `import this` در خط فرمان پیدا کنید.

از جمله چیزهای دیگر، Python به شدت سازگاری در سبک برنامه‌نویسی را ترجیح می‌دهد.

همه ما ضرب‌المثل درباره سازگاری و ذهن‌های کوچک را شنیده‌ایم.

در برنامه‌نویسی، مانند ریاضیات، عکس آن درست است

* یک مقاله ریاضی که در آن نمادهای $\cup$ و $\cap$ معکوس شده باشند، خواندن آن بسیار سخت خواهد بود، حتی اگر نویسنده در صفحه اول به شما بگوید.

در Python، سبک استاندارد در [PEP8](https://peps.python.org/pep-0008/) بیان شده است.

(گاهی اوقات در این سخنرانی‌ها از PEP8 منحرف خواهیم شد تا با نشانه‌گذاری ریاضی بهتر مطابقت داشته باشیم)

(Docstrings)=
### Docstring‌ها

```{index} single: Python; Docstrings
```

Python سیستمی برای اضافه کردن توضیحات به ماژول‌ها، کلاس‌ها، توابع و غیره دارد که *docstring* نامیده می‌شود.

نکته خوب درباره docstring‌ها این است که در زمان اجرا در دسترس هستند.

اجرای این کد را امتحان کنید

```{code-cell} python3
def f(x):
    """
    This function squares its argument
    """
    return x**2
```

پس از اجرای این کد، docstring در دسترس است

```{code-cell} ipython
f?
```

```{code-block} ipython
:class: no-execute

Type:       function
String Form:<function f at 0x2223320>
File:       /home/john/temp/temp.py
Definition: f(x)
Docstring:  This function squares its argument
```

```{code-cell} ipython
f??
```

```{code-block} ipython
:class: no-execute

Type:       function
String Form:<function f at 0x2223320>
File:       /home/john/temp/temp.py
Definition: f(x)
Source:
def f(x):
    """
    This function squares its argument
    """
    return x**2
```

با یک علامت سوال docstring را می‌آوریم، و با دو علامت سوال کد منبع را نیز دریافت می‌کنیم.

می‌توانید قراردادهای docstring را در [PEP257](https://peps.python.org/pep-0257/) پیدا کنید.

## تمرین‌ها

تمرین‌های زیر را حل کنید.

(برای برخی، تابع داخلی `sum()` مفید است).

```{exercise-start}
:label: pyess_ex1
```
قسمت 1: با توجه به دو لیست یا tuple عددی `x_vals` و `y_vals` با طول برابر، ضرب داخلی آن‌ها را با استفاده از `zip()` محاسبه کنید.

قسمت 2: در یک خط، تعداد اعداد زوج در 0,...,99 را بشمارید.

قسمت 3: با توجه به `pairs = ((2, 5), (4, 2), (9, 8), (12, 10))`، تعداد جفت‌های `(a, b)` را بشمارید که در آن هم `a` و هم `b` زوج باشند.

```{hint}
:class: dropdown

`x % 2` اگر `x` زوج باشد 0 برمی‌گرداند، در غیر این صورت 1.

```

```{exercise-end}
```


```{solution-start} pyess_ex1
:class: dropdown
```

**راه‌حل قسمت 1:**

یک راه‌حل ممکن اینجاست

```{code-cell} python3
x_vals = [1, 2, 3]
y_vals = [1, 1, 1]
sum([x * y for x, y in zip(x_vals, y_vals)])
```

این هم کار می‌کند

```{code-cell} python3
sum(x * y for x, y in zip(x_vals, y_vals))
```

**راه‌حل قسمت 2:**

یک راه‌حل اینجاست

```{code-cell} python3
sum([x % 2 == 0 for x in range(100)])
```

این هم کار می‌کند:

```{code-cell} python3
sum(x % 2 == 0 for x in range(100))
```

برخی از جایگزین‌های کمتر طبیعی که با این حال به نشان دادن انعطاف‌پذیری list comprehension‌ها کمک می‌کنند عبارتند از

```{code-cell} python3
len([x for x in range(100) if x % 2 == 0])
```

و

```{code-cell} python3
sum([1 for x in range(100) if x % 2 == 0])
```

**راه‌حل قسمت 3:**

یک احتمال اینجاست

```{code-cell} python3
pairs = ((2, 5), (4, 2), (9, 8), (12, 10))
sum([x % 2 == 0 and y % 2 == 0 for x, y in pairs])
```

```{solution-end}
```

```{exercise-start}
:label: pyess_ex2
```

چندجمله‌ای زیر را در نظر بگیرید

```{math}
:label: polynom0

p(x)
= a_0 + a_1 x + a_2 x^2 + \cdots a_n x^n
= \sum_{i=0}^n a_i x^i
```

تابعی `p` بنویسید به طوری که `p(x, coeff)` مقدار را در {eq}`polynom0` با توجه به نقطه `x` و لیست ضرایب `coeff` ($a_1, a_2, \cdots a_n$) محاسبه کند.

سعی کنید از `enumerate()` در حلقه خود استفاده کنید.

```{exercise-end}
```

```{solution-start} pyess_ex2
:class: dropdown
```
یک راه‌حل اینجاست:

```{code-cell} python3
def p(x, coeff):
    return sum(a * x**i for i, a in enumerate(coeff))
```

```{code-cell} python3
p(1, (2, 4))
```

```{solution-end}
```


```{exercise-start}
:label: pyess_ex3
```

تابعی بنویسید که یک رشته را به عنوان آرگومان می‌گیرد و تعداد حروف بزرگ در رشته را برمی‌گرداند.

```{hint}
:class: dropdown

`'foo'.upper()` مقدار `'FOO'` را برمی‌گرداند.

```

```{exercise-end}
```

```{solution-start} pyess_ex3
:class: dropdown
```

یک راه‌حل اینجاست:

```{code-cell} python3
def f(string):
    count = 0
    for letter in string:
        if letter == letter.upper() and letter.isalpha():
            count += 1
    return count

f('The Rain in Spain')
```

یک جایگزین، راه‌حل pythonic‌تر:

```{code-cell} python3
def count_uppercase_chars(s):
    return sum([c.isupper() for c in s])

count_uppercase_chars('The Rain in Spain')
```

```{solution-end}
```



```{exercise}
:label: pyess_ex4

تابعی بنویسید که دو دنباله `seq_a` و `seq_b` را به عنوان آرگومان می‌گیرد و `True` را برمی‌گرداند اگر هر عنصر در `seq_a` همچنین عنصری از `seq_b` باشد، در غیر این صورت `False`.

* منظور از "دنباله" یک لیست، یک tuple یا یک رشته است.
* تمرین را بدون استفاده از [set‌ها](https://docs.python.org/3/tutorial/datastructures.html#sets) و متدهای set انجام دهید.
```

```{solution-start} pyess_ex4
:class: dropdown
```

یک راه‌حل اینجاست:

```{code-cell} python3
def f(seq_a, seq_b):
    for a in seq_a:
        if a not in seq_b:
            return False
    return True

# == test == #
print(f("ab", "cadb"))
print(f("ab", "cjdb"))
print(f([1, 2], [1, 2, 3]))
print(f([1, 2, 3], [1, 2]))
```

یک جایگزین، راه‌حل pythonic‌تر با استفاده از `all()`:

```{code-cell} python3
def f(seq_a, seq_b):
  return all([i in seq_b for i in seq_a])

# == test == #
print(f("ab", "cadb"))
print(f("ab", "cjdb"))
print(f([1, 2], [1, 2, 3]))
print(f([1, 2, 3], [1, 2]))
```

البته، اگر از نوع داده `sets` استفاده کنیم، راه‌حل ساده‌تر است

```{code-cell} python3
def f(seq_a, seq_b):
    return set(seq_a).issubset(set(seq_b))
```

```{solution-end}
```


```{exercise}
:label: pyess_ex5

وقتی کتابخانه‌های عددی را پوشش دهیم، خواهیم دید که آن‌ها شامل جایگزین‌های زیادی برای درونیابی و تقریب تابع هستند.

با این حال، بیایید روتین تقریب تابع خودمان را به عنوان یک تمرین بنویسیم.

به طور خاص، بدون استفاده از هیچ import، تابعی `linapprox` بنویسید که به عنوان آرگومان‌ها می‌گیرد

* یک تابع `f` که بازه‌ای $[a, b]$ را به $\mathbb R$ نگاشت می‌کند.
* دو اسکالر `a` و `b` که حدود این بازه را مشخص می‌کنند.
* یک عدد صحیح `n` که تعداد نقاط شبکه را تعیین می‌کند.
* یک عدد `x` که `a <= x <= b` را برآورده می‌کند.

و [درونیابی خطی تکه‌ای](https://en.wikipedia.org/wiki/Linear_interpolation) `f` را در `x`، بر اساس `n` نقطه شبکه با فاصله یکسان `a = point[0] < point[1] < ... < point[n-1] = b` برمی‌گرداند.

برای وضوح تلاش کنید، نه کارایی.
```

```{solution-start} pyess_ex5
:class: dropdown
```
یک راه‌حل اینجاست:

```{code-cell} python3
def linapprox(f, a, b, n, x):
    """
    Evaluates the piecewise linear interpolant of f at x on the interval
    [a, b], with n evenly spaced grid points.

    Parameters
    ==========
        f : function
            The function to approximate

        x, a, b : scalars (floats or integers)
            Evaluation point and endpoints, with a <= x <= b

        n : integer
            Number of grid points

    Returns
    =======
        A float. The interpolant evaluated at x

    """
    length_of_interval = b - a
    num_subintervals = n - 1
    step = length_of_interval / num_subintervals

    # === find first grid point larger than x === #
    point = a
    while point <= x:
        point += step

    # === x must lie between the gridpoints (point - step) and point === #
    u, v = point - step, point

    return f(u) + (x - u) * (f(v) - f(u)) / (v - u)
```

```{solution-end}
```


```{exercise-start}
:label: pyess_ex6
```

با استفاده از نحو list comprehension، می‌توانیم حلقه در کد زیر را ساده کنیم.

```{code-cell} python3
import numpy as np

n = 100
ϵ_values = []
for i in range(n):
    e = np.random.randn()
    ϵ_values.append(e)
```

```{exercise-end}
```

```{solution-start} pyess_ex6
:class: dropdown
```

یک راه‌حل اینجاست.

```{code-cell} python3
n = 100
ϵ_values = [np.random.randn() for i in range(n)]
```

```{solution-end}
```