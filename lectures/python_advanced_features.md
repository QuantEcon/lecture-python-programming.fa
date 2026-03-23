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
  More Language Features: ویژگی‌های بیشتر زبان
  Overview: مروری کلی
  Iterables and iterators: تکرارپذیرها و تکرارکننده‌ها
  Iterables and iterators::Iterators: تکرارکننده‌ها
  Iterables and iterators::Iterators in for loops: تکرارکننده‌ها در حلقه‌های for
  Iterables and iterators::Iterables: تکرارپذیرها
  Iterables and iterators::Iterators and built-ins: تکرارکننده‌ها و توابع توکار
  '`*` and `**` operators': عملگرهای `*` و `**`
  '`*` and `**` operators::Unpacking arguments': باز کردن آرگومان‌ها
  '`*` and `**` operators::Arbitrary arguments': آرگومان‌های دلخواه
  Type hints: راهنمای نوع
  Type hints::Basic syntax: نحو پایه
  Type hints::Common types: نوع‌های رایج
  Type hints::Hints don't enforce types: راهنماها نوع را اعمال نمی‌کنند
  Type hints::Why use type hints?: چرا از راهنمای نوع استفاده کنیم؟
  Type hints::Type hints in scientific Python: راهنمای نوع در Python علمی
  Decorators and descriptors: دکوراتورها و توصیف‌گرها
  Decorators and descriptors::Decorators: دکوراتورها
  Decorators and descriptors::Decorators::An example: یک مثال
  Decorators and descriptors::Decorators::Enter decorators: ورود دکوراتورها
  Decorators and descriptors::Descriptors: توصیف‌گرها
  Decorators and descriptors::Descriptors::A solution: یک راه‌حل
  Decorators and descriptors::Descriptors::How it works: چگونگی کارکرد آن
  Decorators and descriptors::Descriptors::Decorators and properties: دکوراتورها و ویژگی‌ها
  Generators: Generatorها
  Generators::Generator expressions: عبارات Generator
  Generators::Generator functions: توابع Generator
  Generators::Generator functions::Example 1: مثال 1
  Generators::Generator functions::Example 2: مثال 2
  Generators::Advantages of iterators: مزایای Iteratorها
  Exercises: تمرین‌ها
---

(python_advanced_features)=
```{raw} jupyter
<div id="qe-notebook-header" align="right" style="text-align:right;">
        <a href="https://quantecon.org/" title="quantecon.org">
                <img style="width:250px;display:inline;" width="250px" src="https://assets.quantecon.org/img/qe-menubar-logo.svg" alt="QuantEcon">
        </a>
</div>
```

# ویژگی‌های بیشتر زبان

(python_advanced_features)=
```{raw} jupyter
<div id="qe-notebook-header" align="right" style="text-align:right;">
        <a href="https://quantecon.org/" title="quantecon.org">
                <img style="width:250px;display:inline;" width="250px" src="https://assets.quantecon.org/img/qe-menubar-logo.svg" alt="QuantEcon">
        </a>
</div>
```

# ویژگی‌های بیشتر زبان

## مروری کلی

توصیه ما برای این سخنرانی آخر این است که **در مرور اول آن را رد کنید**، مگر اینکه میل شدیدی به خواندن آن داشته باشید.

این سخنرانی اینجاست

1. به عنوان مرجع، تا بتوانیم در صورت نیاز به آن لینک دهیم، و
1. برای کسانی که تعدادی از کاربردها را انجام داده‌اند و اکنون می‌خواهند بیشتر درباره زبان Python یاد بگیرند

موضوعات متنوعی در این سخنرانی بررسی می‌شوند، از جمله iteratorها، decoratorها و descriptorها، و generatorها.

## تکرارپذیرها و تکرارکننده‌ها

```{index} single: Python; Iteration
```

ما {ref}`قبلاً چیزهایی <iterating_version_1>` درباره تکرار در Python گفته‌ایم.

حالا بیایید نگاه دقیق‌تری به نحوه عملکرد آن داشته باشیم و بر پیاده‌سازی حلقه `for` در Python تمرکز کنیم.

(iterators)=

### تکرارکننده‌ها

```{index} single: Python; Iterators
```

تکرارکننده‌ها یک رابط یکنواخت برای گام برداشتن از میان عناصر یک مجموعه هستند.

در اینجا درباره استفاده از تکرارکننده‌ها صحبت خواهیم کرد --- بعداً یاد می‌گیریم چگونه تکرارکننده‌های خود را بسازیم.

به طور رسمی، یک *تکرارکننده* شیئی است که دارای متد `__next__` است.

به عنوان مثال، اشیاء فایل تکرارکننده هستند.

برای مشاهده این موضوع، بیایید نگاه دیگری به {ref}`داده‌های شهرهای آمریکا <us_cities_data>` داشته باشیم،
که در سلول زیر در پوشه کاری جاری نوشته می‌شود

```{code-cell} ipython
%%file us_cities.txt
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

```{code-cell} python3
f = open('us_cities.txt')
f.__next__()
```

```{code-cell} python3
f.__next__()
```

می‌بینیم که اشیاء فایل واقعاً دارای متد `__next__` هستند و فراخوانی این متد خط بعدی فایل را برمی‌گرداند.

متد next همچنین از طریق تابع توکار `next()` نیز قابل دسترسی است،
که مستقیماً این متد را فراخوانی می‌کند

```{code-cell} python3
next(f)
```

اشیاء بازگردانده‌شده توسط `enumerate()` نیز تکرارکننده هستند

```{code-cell} python3
e = enumerate(['foo', 'bar'])
next(e)
```

```{code-cell} python3
next(e)
```

اشیاء reader از ماژول `csv` نیز همین‌طور هستند.

بیایید یک فایل csv کوچک حاوی داده‌های شاخص NIKKEI بسازیم

```{code-cell} ipython
%%file test_table.csv
Date,Open,High,Low,Close,Volume,Adj Close
2009-05-21,9280.35,9286.35,9189.92,9264.15,133200,9264.15
2009-05-20,9372.72,9399.40,9311.61,9344.64,143200,9344.64
2009-05-19,9172.56,9326.75,9166.97,9290.29,167000,9290.29
2009-05-18,9167.05,9167.82,8997.74,9038.69,147800,9038.69
2009-05-15,9150.21,9272.08,9140.90,9265.02,172000,9265.02
2009-05-14,9212.30,9223.77,9052.41,9093.73,169400,9093.73
2009-05-13,9305.79,9379.47,9278.89,9340.49,176000,9340.49
2009-05-12,9358.25,9389.61,9298.61,9298.61,188400,9298.61
2009-05-11,9460.72,9503.91,9342.75,9451.98,230800,9451.98
2009-05-08,9351.40,9464.43,9349.57,9432.83,220200,9432.83
```

```{code-cell} python3
from csv import reader

f = open('test_table.csv', 'r')
nikkei_data = reader(f)
next(nikkei_data)
```

```{code-cell} python3
next(nikkei_data)
```

### تکرارکننده‌ها در حلقه‌های for

```{index} single: Python; Iterators
```

تمام تکرارکننده‌ها می‌توانند در سمت راست کلیدواژه `in` در دستورات حلقه `for` قرار گیرند.

در واقع حلقه `for` به همین شکل کار می‌کند: اگر بنویسیم

```{code-block} python3
:class: no-execute

for x in iterator:
    <code block>
```

آنگاه مفسر

* `iterator.___next___()` را فراخوانی می‌کند و `x` را به نتیجه متصل می‌کند
* بلوک کد را اجرا می‌کند
* تا زمانی که خطای `StopIteration` رخ دهد تکرار می‌کند

پس حالا می‌دانید این نحو جادویی چگونه کار می‌کند

```{code-block} python3
:class: no-execute

f = open('somefile.txt', 'r')
for line in f:
    # do something
```

مفسر فقط به طور مداوم

1. `f.__next__()` را فراخوانی می‌کند و `line` را به نتیجه متصل می‌کند
1. بدنه حلقه را اجرا می‌کند

این تا زمان وقوع خطای `StopIteration` ادامه می‌یابد.

### تکرارپذیرها

```{index} single: Python; Iterables
```

شما از قبل می‌دانید که می‌توانیم یک لیست Python را در سمت راست `in` در حلقه `for` قرار دهیم

```{code-cell} python3
for i in ['spam', 'eggs']:
    print(i)
```

پس آیا این به این معنی است که یک لیست تکرارکننده است؟

پاسخ خیر است

```{code-cell} python3
x = ['foo', 'bar']
type(x)
```

```{code-cell} python3
---
tags: [raises-exception]
---
next(x)
```

پس چرا می‌توانیم روی یک لیست در حلقه `for` تکرار کنیم؟

دلیل این است که یک لیست *تکرارپذیر* است (در مقابل تکرارکننده).

به طور رسمی، یک شیء تکرارپذیر است اگر بتوان آن را با استفاده از تابع توکار `iter()` به تکرارکننده تبدیل کرد.

لیست‌ها یکی از این اشیاء هستند

```{code-cell} python3
x = ['foo', 'bar']
type(x)
```

```{code-cell} python3
y = iter(x)
type(y)
```

```{code-cell} python3
next(y)
```

```{code-cell} python3
next(y)
```

```{code-cell} python3
---
tags: [raises-exception]
---
next(y)
```

بسیاری از اشیاء دیگر نیز تکرارپذیر هستند، مانند دیکشنری‌ها و تاپل‌ها.

البته، همه اشیاء تکرارپذیر نیستند

```{code-cell} python3
---
tags: [raises-exception]
---
iter(42)
```

برای جمع‌بندی بحث ما درباره حلقه‌های `for`

* حلقه‌های `for` روی تکرارکننده‌ها یا تکرارپذیرها کار می‌کنند.
* در حالت دوم، تکرارپذیر قبل از شروع حلقه به تکرارکننده تبدیل می‌شود.

### تکرارکننده‌ها و توابع توکار

```{index} single: Python; Iterators
```

برخی از توابع توکار که روی دنباله‌ها عمل می‌کنند با تکرارپذیرها نیز کار می‌کنند

* `max()`, `min()`, `sum()`, `all()`, `any()`

به عنوان مثال

```{code-cell} python3
x = [10, -10]
max(x)
```

```{code-cell} python3
y = iter(x)
type(y)
```

```{code-cell} python3
max(y)
```

یک نکته‌ای که باید درباره تکرارکننده‌ها به خاطر بسپارید این است که با استفاده تخلیه می‌شوند

```{code-cell} python3
x = [10, -10]
y = iter(x)
max(y)
```

```{code-cell} python3
---
tags: [raises-exception]
---
max(y)
```

## عملگرهای `*` و `**`

`*` و `**` ابزارهای مفید و پرکاربردی هستند که برای باز کردن لیست‌ها و تاپل‌ها و همچنین برای تعریف توابعی که آرگومان‌های دلخواه را به عنوان ورودی دریافت می‌کنند، به کار می‌روند.

در این بخش، نحوه استفاده از آن‌ها را بررسی کرده و موارد استفاده‌شان را از هم تمیز می‌دهیم.

### باز کردن آرگومان‌ها

هنگامی که با یک لیست از پارامترها کار می‌کنیم، اغلب لازم است محتوای لیست را به جای یک مجموعه، به صورت آرگومان‌های مجزا هنگام ارسال به توابع استخراج کنیم.

خوشبختانه، عملگر `*` می‌تواند به ما کمک کند تا لیست‌ها و تاپل‌ها را به [*آرگومان‌های موضعی*](pos_args) در فراخوانی توابع باز کنیم.

برای ملموس‌تر شدن موضوع، مثال‌های زیر را در نظر بگیرید:

بدون `*`، تابع `print` یک لیست را چاپ می‌کند

```{code-cell} python3
l1 = ['a', 'b', 'c']

print(l1)
```

در حالی که تابع `print` عناصر منفرد را چاپ می‌کند، زیرا `*` لیست را به آرگومان‌های مجزا باز می‌کند

```{code-cell} python3
print(*l1)
```

باز کردن لیست با استفاده از `*` به آرگومان‌های موضعی معادل تعریف جداگانه آن‌ها هنگام فراخوانی تابع است

```{code-cell} python3
print('a', 'b', 'c')
```

اما عملگر `*` در صورتی که بخواهیم دوباره از آن‌ها استفاده کنیم، راحت‌تر است

```{code-cell} python3
l1.append('d')

print(*l1)
```

به طور مشابه، `**` برای باز کردن آرگومان‌ها استفاده می‌شود.

تفاوت در این است که `**` *دیکشنری‌ها* را به *آرگومان‌های کلیدواژه‌ای* باز می‌کند.

`**` اغلب زمانی استفاده می‌شود که آرگومان‌های کلیدواژه‌ای زیادی داریم که می‌خواهیم دوباره از آن‌ها استفاده کنیم.

برای مثال، فرض کنید می‌خواهیم چندین نمودار با تنظیمات گرافیکی یکسان رسم کنیم،
که ممکن است شامل تنظیم مکرر پارامترهای گرافیکی زیادی باشد که معمولاً با آرگومان‌های کلیدواژه‌ای تعریف می‌شوند.

در این حالت، می‌توانیم از یک دیکشنری برای ذخیره این پارامترها استفاده کنیم و از `**` برای باز کردن دیکشنری‌ها به آرگومان‌های کلیدواژه‌ای هنگام نیاز بهره ببریم.

بیایید با هم یک مثال ساده را دنبال کنیم و کاربرد `*` و `**` را از هم تمیز دهیم

```{code-cell} python3
import numpy as np
import matplotlib.pyplot as plt

# Set up the frame and subplots
fig, ax = plt.subplots(2, 1)
plt.subplots_adjust(hspace=0.7)

# Create a function that generates synthetic data
def generate_data(β_0, β_1, σ=30, n=100):
    x_values = np.arange(0, n, 1)
    y_values = β_0 + β_1 * x_values + np.random.normal(size=n, scale=σ)
    return x_values, y_values

# Store the keyword arguments for lines and legends in a dictionary
line_kargs = {'lw': 1.5, 'alpha': 0.7}
legend_kargs = {'bbox_to_anchor': (0., 1.02, 1., .102), 
                'loc': 3, 
                'ncol': 4,
                'mode': 'expand', 
                'prop': {'size': 7}}

β_0s = [10, 20, 30]
β_1s = [1, 2, 3]

# Use a for loop to plot lines
def generate_plots(β_0s, β_1s, idx, line_kargs, legend_kargs):
    label_list = []
    for βs in zip(β_0s, β_1s):
    
        # Use * to unpack tuple βs and the tuple output from the generate_data function
        # Use ** to unpack the dictionary of keyword arguments for lines
        ax[idx].plot(*generate_data(*βs), **line_kargs)

        label_list.append(f'$β_0 = {βs[0]}$ | $β_1 = {βs[1]}$')

    # Use ** to unpack the dictionary of keyword arguments for legends
    ax[idx].legend(label_list, **legend_kargs)

generate_plots(β_0s, β_1s, 0, line_kargs, legend_kargs)

# We can easily reuse and update our parameters
β_1s.append(-2)
β_0s.append(40)
line_kargs['lw'] = 2
line_kargs['alpha'] = 0.4

generate_plots(β_0s, β_1s, 1, line_kargs, legend_kargs)
plt.show()
```

در این مثال، `*` پارامترهای زیپ‌شده `βs` و خروجی تابع `generate_data` که در تاپل‌ها ذخیره شده بودند را باز کرد،
در حالی که `**` پارامترهای گرافیکی ذخیره‌شده در `legend_kargs` و `line_kargs` را باز کرد.

در خلاصه، هنگامی که `*list`/`*tuple` و `**dictionary` به *فراخوانی توابع* ارسال می‌شوند، به جای یک مجموعه، به آرگومان‌های مجزا باز می‌شوند.

تفاوت در این است که `*` لیست‌ها و تاپل‌ها را به *آرگومان‌های موضعی* باز می‌کند، در حالی که `**` دیکشنری‌ها را به *آرگومان‌های کلیدواژه‌ای* باز می‌کند.

### آرگومان‌های دلخواه

هنگامی که توابع را *تعریف* می‌کنیم، گاهی اوقات مطلوب است به کاربران اجازه داده شود هر تعداد آرگومان که می‌خواهند به تابع بدهند.

شاید متوجه شده باشید که تابع `ax.plot()` می‌تواند با تعداد دلخواه آرگومان کار کند.

اگر به [مستندات](https://github.com/matplotlib/matplotlib/blob/v3.6.2/lib/matplotlib/axes/_axes.py#L1417-L1669) تابع نگاه کنیم، می‌بینیم که تابع به صورت زیر تعریف شده است

```
Axes.plot(*args, scalex=True, scaley=True, data=None, **kwargs)
```

عملگرهای `*` و `**` را دوباره در زمینه *تعریف تابع* مشاهده می‌کنیم.

در واقع، `*args` و `**kargs` در کتابخانه‌های علمی پایتون برای کاهش تکرار و امکان ورودی‌های انعطاف‌پذیر فراگیر هستند.

`*args` به تابع امکان می‌دهد *آرگومان‌های موضعی* با اندازه متغیر را مدیریت کند

```{code-cell} python3
l1 = ['a', 'b', 'c']
l2 = ['b', 'c', 'd']

def arb(*ls):
    print(ls)

arb(l1, l2)
```

ورودی‌ها به تابع ارسال شده و در یک تاپل ذخیره می‌شوند.

بیایید ورودی‌های بیشتری امتحان کنیم

```{code-cell} python3
l3 = ['z', 'x', 'b']
arb(l1, l2, l3)
```

به طور مشابه، پایتون به ما اجازه می‌دهد از `**kargs` برای ارسال تعداد دلخواه *آرگومان‌های کلیدواژه‌ای* به توابع استفاده کنیم

```{code-cell} python3
def arb(**ls):
    print(ls)

# Note that these are keyword arguments
arb(l1=l1, l2=l2)
```

می‌بینیم که پایتون از یک دیکشنری برای ذخیره این آرگومان‌های کلیدواژه‌ای استفاده می‌کند.

بیایید ورودی‌های بیشتری امتحان کنیم

```{code-cell} python3
arb(l1=l1, l2=l2, l3=l3)
```

به طور کلی، `*args` و `**kargs` هنگام *تعریف یک تابع* استفاده می‌شوند؛ آن‌ها به تابع امکان می‌دهند ورودی با اندازه دلخواه دریافت کند.

تفاوت در این است که توابع با `*args` می‌توانند *آرگومان‌های موضعی* با اندازه دلخواه دریافت کنند، در حالی که `**kargs` به توابع اجازه می‌دهد تعداد دلخواه *آرگومان‌های کلیدواژه‌ای* دریافت کنند.

## راهنمای نوع

```{index} single: Python; Type Hints
```

Python یک زبان *با نوع‌دهی پویا* است، به این معنا که نیازی به اعلان نوع متغیرها ندارید.

(بحث {doc}`قبلی ما <need_for_speed>` درباره نوع‌های پویا در مقابل ایستا را ببینید.)

با این حال، Python از **راهنمای نوع** اختیاری (که به عنوان حاشیه‌نویسی نوع نیز شناخته می‌شود) پشتیبانی می‌کند که به شما اجازه می‌دهد نوع‌های مورد انتظار متغیرها، پارامترهای تابع و مقادیر بازگشتی را مشخص کنید.

راهنمای نوع از Python 3.5 معرفی شد و در نسخه‌های بعدی تکامل یافت.
تمام نحو نشان داده شده در اینجا در Python 3.9 و نسخه‌های بعدی کار می‌کند.

```{note}
راهنمای نوع *در زمان اجرا توسط مفسر Python نادیده گرفته می‌شود* --- آن‌ها بر نحوه اجرای کد شما تأثیر نمی‌گذارند. آن‌ها صرفاً اطلاعاتی هستند و به عنوان مستندات برای انسان‌ها و ابزارها عمل می‌کنند.
```

### نحو پایه

راهنمای نوع از دو نقطه `:` برای حاشیه‌نویسی متغیرها و پارامترها، و از پیکان `->` برای حاشیه‌نویسی نوع‌های بازگشتی استفاده می‌کند.

در اینجا یک مثال ساده آورده شده است:

```{code-cell} python3
def greet(name: str, times: int) -> str:
    return (name + '! ') * times

greet('hello', 3)
```

در این تعریف تابع:

- `name: str` نشان می‌دهد که `name` انتظار می‌رود یک رشته باشد
- `times: int` نشان می‌دهد که `times` انتظار می‌رود یک عدد صحیح باشد
- `-> str` نشان می‌دهد که تابع یک رشته برمی‌گرداند

همچنین می‌توانید متغیرها را مستقیماً حاشیه‌نویسی کنید:

```{code-cell} python3
x: int = 10
y: float = 3.14
name: str = 'Python'
```

### نوع‌های رایج

پرکاربردترین راهنماهای نوع، نوع‌های داخلی هستند:

| نوع      | مثال                          |
|-----------|----------------------------------|
| `int`     | `x: int = 5`                    |
| `float`   | `x: float = 3.14`              |
| `str`     | `x: str = 'hello'`             |
| `bool`    | `x: bool = True`               |
| `list`    | `x: list = [1, 2, 3]`          |
| `dict`    | `x: dict = {'a': 1}`           |

برای ظرف‌ها، می‌توانید نوع‌های عناصر آن‌ها را مشخص کنید:

```{code-cell} python3
prices: list[float] = [9.99, 4.50, 2.89]
counts: dict[str, int] = {'apples': 3, 'oranges': 5}
```

### راهنماها نوع را اعمال نمی‌کنند

یک نکته مهم برای برنامه‌نویسان جدید Python: راهنمای نوع در زمان اجرا *اعمال نمی‌شود*.

Python در صورتی که نوع «اشتباه» را ارسال کنید خطایی ایجاد نمی‌کند:

```{code-cell} python3
def add(x: int, y: int) -> int:
    return x + y

# Passes floats — Python doesn't complain
add(1.5, 2.7)
```

راهنماها `int` می‌گویند، اما Python با خوشحالی آرگومان‌های `float` را می‌پذیرد و `4.2` را برمی‌گرداند --- که آن هم `int` نیست.

این تفاوت اساسی با زبان‌های با نوع‌دهی ایستا مانند C یا Java است، که در آن‌ها عدم تطابق نوع‌ها باعث خطاهای کامپایل می‌شود.

### چرا از راهنمای نوع استفاده کنیم؟

اگر Python آن‌ها را نادیده می‌گیرد، چرا زحمت بکشیم؟

1. **خوانایی**: راهنمای نوع امضای تابع را خودمستندساز می‌کند. خواننده فوراً می‌داند که تابع چه نوع‌هایی را انتظار دارد و چه چیزی برمی‌گرداند.
2. **پشتیبانی ویرایشگر**: محیط‌های توسعه یکپارچه مانند VS Code از راهنمای نوع برای ارائه تکمیل خودکار بهتر، تشخیص خطا و مستندات درخطی استفاده می‌کنند.
3. **بررسی خطا**: ابزارهایی مانند [mypy](https://mypy.readthedocs.io/) و [pyrefly](https://pyrefly.org/) راهنمای نوع را تحلیل می‌کنند تا اشکالات را *قبل از* اجرای کد شما شناسایی کنند.
4. **کد تولیدشده توسط مدل‌های زبانی بزرگ**: مدل‌های زبانی بزرگ اغلب کدی با راهنمای نوع تولید می‌کنند، بنابراین درک نحو به شما کمک می‌کند خروجی آن‌ها را بخوانید و استفاده کنید.

### راهنمای نوع در Python علمی

راهنمای نوع به بحث {doc}`نیاز به سرعت <need_for_speed>` مرتبط است:

* کتابخانه‌های پرعملکرد مانند [JAX](https://jax.readthedocs.io/) و [Numba](https://numba.pydata.org/) برای کامپایل کد ماشین سریع به دانستن نوع متغیرها متکی هستند.
* در حالی که این کتابخانه‌ها نوع‌ها را در زمان اجرا استنتاج می‌کنند نه اینکه مستقیماً راهنمای نوع Python را بخوانند، *مفهوم* یکسان است --- اطلاعات صریح نوع، بهینه‌سازی را ممکن می‌سازد.
* با تکامل اکوسیستم Python، انتظار می‌رود ارتباط بین راهنمای نوع و ابزارهای عملکرد رشد کند.

در حال حاضر، مزیت اصلی راهنمای نوع در Python روزانه *وضوح و پشتیبانی ابزار* است که با رشد اندازه برنامه‌ها ارزش آن به طور فزاینده‌ای افزایش می‌یابد.

## دکوراتورها و توصیف‌گرها

```{index} single: Python; Decorators
```

```{index} single: Python; Descriptors
```

بیایید برخی از عناصر نحوی خاصی را که به‌طور معمول توسط برنامه‌نویسان پایتون استفاده می‌شود، بررسی کنیم.

ممکن است به مفاهیم زیر بلافاصله نیاز نداشته باشید، اما آن‌ها را در کدهای دیگران خواهید دید.

از این رو، در مرحله‌ای از آموزش پایتون خود باید آن‌ها را درک کنید.

### دکوراتورها

```{index} single: Python; Decorators
```

دکوراتورها نوعی قند نحوی هستند که هرچند به‌راحتی می‌توان از آن‌ها اجتناب کرد، اما محبوبیت زیادی پیدا کرده‌اند.

توضیح اینکه دکوراتورها چه می‌کنند بسیار آسان است.

از سوی دیگر، توضیح *چرایی* استفاده از آن‌ها کمی تلاش می‌طلبد.

#### یک مثال

فرض کنید روی برنامه‌ای کار می‌کنیم که چیزی شبیه به این است

```{code-cell} python3
import numpy as np

def f(x):
    return np.log(np.log(x))

def g(x):
    return np.sqrt(42 * x)

# Program continues with various calculations using f and g
```

حال فرض کنید مشکلی وجود دارد: گاهی اوقات اعداد منفی در محاسباتی که پس از این می‌آیند به `f` و `g` داده می‌شوند.

اگر امتحان کنید، خواهید دید که وقتی این توابع با اعداد منفی فراخوانی می‌شوند، یک شیء NumPy به نام `nan` برمی‌گردانند.

این مخفف "not a number" (عدد نیست) است (و نشان می‌دهد که سعی دارید یک تابع ریاضی را در نقطه‌ای که تعریف نشده ارزیابی کنید).

شاید این همان چیزی نباشد که می‌خواهیم، زیرا مشکلات دیگری ایجاد می‌کند که بعداً شناسایی آن‌ها دشوار است.

فرض کنید به جای آن می‌خواهیم برنامه هر بار که این اتفاق می‌افتد با یک پیغام خطای معنادار پایان یابد.

پیاده‌سازی این تغییر به اندازه کافی آسان است

```{code-cell} python3
import numpy as np

def f(x):
    assert x >= 0, "Argument must be nonnegative"
    return np.log(np.log(x))

def g(x):
    assert x >= 0, "Argument must be nonnegative"
    return np.sqrt(42 * x)

# Program continues with various calculations using f and g
```

اما توجه کنید که در اینجا تکراری وجود دارد، به شکل دو خط کد یکسان.

تکرار کد ما را طولانی‌تر و نگهداری آن را سخت‌تر می‌کند، و از این رو چیزی است که سعی می‌کنیم از آن اجتناب کنیم.

در اینجا این موضوع مشکل بزرگی نیست، اما حالا تصور کنید که به جای فقط `f` و `g`، ۲۰ تابع داریم که باید به روش کاملاً یکسانی تغییر دهیم.

این بدان معناست که باید منطق آزمون (یعنی خط `assert` که غیرمنفی بودن را بررسی می‌کند) را ۲۰ بار تکرار کنیم.

وضعیت اگر منطق آزمون طولانی‌تر و پیچیده‌تر باشد بدتر هم می‌شود.

در این نوع سناریو، رویکرد زیر منظم‌تر خواهد بود

```{code-cell} python3
import numpy as np

def check_nonneg(func):
    def safe_function(x):
        assert x >= 0, "Argument must be nonnegative"
        return func(x)
    return safe_function

def f(x):
    return np.log(np.log(x))

def g(x):
    return np.sqrt(42 * x)

f = check_nonneg(f)
g = check_nonneg(g)
# Program continues with various calculations using f and g
```

این پیچیده به نظر می‌رسد پس بیایید آن را به آرامی بررسی کنیم.

برای درک منطق، در نظر بگیرید چه اتفاقی می‌افتد وقتی می‌نویسیم `f = check_nonneg(f)`.

این تابع `check_nonneg` را با پارامتر `func` که برابر `f` تنظیم شده فراخوانی می‌کند.

حالا `check_nonneg` یک تابع جدید به نام `safe_function` می‌سازد که `x` را به عنوان غیرمنفی تأیید می‌کند و سپس `func` را روی آن فراخوانی می‌کند (که همان `f` است).

در نهایت، نام سراسری `f` برابر `safe_function` تنظیم می‌شود.

حالا رفتار `f` همان‌طور که می‌خواهیم است، و همین موضوع برای `g` نیز صادق است.

در عین حال، منطق آزمون فقط یک‌بار نوشته شده است.

#### ورود دکوراتورها

```{index} single: Python; Decorators
```

آخرین نسخه کد ما هنوز ایده‌آل نیست.

برای مثال، اگر کسی کد ما را می‌خواند و می‌خواهد بداند `f` چگونه کار می‌کند، به دنبال تعریف تابع می‌گردد، که این است

```{code-cell} python3
def f(x):
    return np.log(np.log(x))
```

ممکن است خط `f = check_nonneg(f)` را نادیده بگیرد.

به همین دلایل و دلایل دیگر، دکوراتورها به پایتون اضافه شدند.

با دکوراتورها، می‌توانیم خطوط زیر را

```{code-cell} python3
def f(x):
    return np.log(np.log(x))

def g(x):
    return np.sqrt(42 * x)

f = check_nonneg(f)
g = check_nonneg(g)
```

با این جایگزین کنیم

```{code-cell} python3
@check_nonneg
def f(x):
    return np.log(np.log(x))

@check_nonneg
def g(x):
    return np.sqrt(42 * x)
```

این دو قطعه کد دقیقاً همان کار را انجام می‌دهند.

اگر همان کار را انجام می‌دهند، آیا واقعاً به نحو دکوراتور نیاز داریم؟

خب، توجه کنید که دکوراتورها درست بالای تعاریف تابع قرار دارند.

از این رو هر کسی که به تعریف تابع نگاه می‌کند آن‌ها را می‌بیند و از اینکه تابع تغییر یافته آگاه می‌شود.

به عقیده بسیاری از افراد، این نحو دکوراتور بهبود قابل توجهی برای زبان است.

(descriptors)=

### توصیف‌گرها

```{index} single: Python; Descriptors
```

توصیف‌گرها یک مشکل رایج در مدیریت متغیرها را حل می‌کنند.

برای درک مسئله، یک کلاس `Car` را در نظر بگیرید که یک خودرو را شبیه‌سازی می‌کند.

فرض کنید این کلاس متغیرهای `miles` و `kms` را تعریف می‌کند که به ترتیب مسافت طی شده را به مایل و کیلومتر نشان می‌دهند.

یک نسخه بسیار ساده‌شده از این کلاس ممکن است به شکل زیر باشد

```{code-cell} python3
class Car:

    def __init__(self, miles=1000):
        self.miles = miles
        self.kms = miles * 1.61

    # Some other functionality, details omitted
```

یک مشکل بالقوه‌ای که ممکن است داشته باشیم این است که کاربر یکی از این متغیرها را تغییر دهد اما نه دیگری را

```{code-cell} python3
car = Car()
car.miles
```

```{code-cell} python3
car.kms
```

```{code-cell} python3
car.miles = 6000
car.kms
```

در دو خط آخر می‌بینیم که `miles` و `kms` هماهنگ نیستند.

آنچه واقعاً می‌خواهیم مکانیزمی است که هر بار کاربر یکی از این متغیرها را تنظیم می‌کند، *دیگری به‌طور خودکار به‌روزرسانی شود*.

#### یک راه‌حل

در پایتون، این مسئله با استفاده از *توصیف‌گرها* حل می‌شود.

یک توصیف‌گر فقط یک شیء پایتون است که متدهای خاصی را پیاده‌سازی می‌کند.

این متدها زمانی فعال می‌شوند که از طریق نمادگذاری دات به شیء دسترسی پیدا کنید.

بهترین راه برای درک این موضوع دیدن آن در عمل است.

این نسخه جایگزین از کلاس `Car` را در نظر بگیرید

```{code-cell} python3
class Car:

    def __init__(self, miles=1000):
        self._miles = miles
        self._kms = miles * 1.61

    def set_miles(self, value):
        self._miles = value
        self._kms = value * 1.61

    def set_kms(self, value):
        self._kms = value
        self._miles = value / 1.61

    def get_miles(self):
        return self._miles

    def get_kms(self):
        return self._kms

    miles = property(get_miles, set_miles)
    kms = property(get_kms, set_kms)
```

ابتدا بیایید بررسی کنیم که رفتار مطلوب را به دست می‌آوریم

```{code-cell} python3
car = Car()
car.miles
```

```{code-cell} python3
car.miles = 6000
car.kms
```

بله، این همان چیزی است که می‌خواهیم --- `car.kms` به‌طور خودکار به‌روزرسانی می‌شود.

#### چگونگی کارکرد آن

نام‌های `_miles` و `_kms` نام‌های دلخواهی هستند که برای ذخیره مقادیر متغیرها استفاده می‌کنیم.

اشیاء `miles` و `kms` *ویژگی‌ها* (properties) هستند، که نوع رایجی از توصیف‌گر به شمار می‌روند.

متدهای `get_miles`، `set_miles`، `get_kms` و `set_kms` تعریف می‌کنند که وقتی این متغیرها را دریافت می‌کنید (یعنی دسترسی پیدا می‌کنید) یا تنظیم می‌کنید (مقیدسازی) چه اتفاقی می‌افتد

* به اصطلاح متدهای "getter" و "setter".

تابع توکار پایتون `property` متدهای getter و setter را می‌گیرد و یک ویژگی می‌سازد.

برای مثال، پس از اینکه `car` به عنوان یک نمونه از `Car` ساخته می‌شود، شیء `car.miles` یک ویژگی است.

از آنجا که یک ویژگی است، وقتی مقدار آن را از طریق `car.miles = 6000` تنظیم می‌کنیم، متد setter آن فعال می‌شود --- در این حالت `set_miles`.

#### دکوراتورها و ویژگی‌ها

```{index} single: Python; Decorators
```

```{index} single: Python; Properties
```

این روزها بسیار رایج است که تابع `property` از طریق یک دکوراتور استفاده شود.

این نسخه دیگری از کلاس `Car` ما است که مثل قبل کار می‌کند اما حالا از دکوراتورها برای تنظیم ویژگی‌ها استفاده می‌کند

```{code-cell} python3
class Car:

    def __init__(self, miles=1000):
        self._miles = miles
        self._kms = miles * 1.61

    @property
    def miles(self):
        return self._miles

    @property
    def kms(self):
        return self._kms

    @miles.setter
    def miles(self, value):
        self._miles = value
        self._kms = value * 1.61

    @kms.setter
    def kms(self, value):
        self._kms = value
        self._miles = value / 1.61
```

در اینجا تمام جزئیات را بررسی نخواهیم کرد.

برای اطلاعات بیشتر می‌توانید به [مستندات توصیف‌گر](https://docs.python.org/3/howto/descriptor.html) مراجعه کنید.

(paf_generators)=

## Generatorها

```{index} single: Python; Generators
```

یک generator نوعی iterator است (یعنی با تابع `next` کار می‌کند).

دو راه برای ساخت generatorها مطالعه خواهیم کرد: عبارات generator و توابع generator.

### عبارات Generator

ساده‌ترین راه برای ساخت generatorها استفاده از *عبارات generator* است.

درست مانند list comprehension، اما با پرانتز گرد.

اینجا list comprehension است:

```{code-cell} python3
singular = ('dog', 'cat', 'bird')
type(singular)
```

```{code-cell} python3
plural = [string + 's' for string in singular]
plural
```

```{code-cell} python3
type(plural)
```

و اینجا عبارت generator است

```{code-cell} python3
singular = ('dog', 'cat', 'bird')
plural = (string + 's' for string in singular)
type(plural)
```

```{code-cell} python3
next(plural)
```

```{code-cell} python3
next(plural)
```

```{code-cell} python3
next(plural)
```

از آنجایی که `sum()` می‌تواند روی iteratorها فراخوانی شود، می‌توانیم این کار را انجام دهیم

```{code-cell} python3
sum((x * x for x in range(10)))
```

تابع `sum()` برای دریافت آیتم‌ها، `next()` را فراخوانی می‌کند و جملات متوالی را جمع می‌کند.

در واقع، می‌توانیم در این مورد پرانتزهای بیرونی را حذف کنیم

```{code-cell} python3
sum(x * x for x in range(10))
```

### توابع Generator

```{index} single: Python; Generator Functions
```

انعطاف‌پذیرترین راه برای ایجاد اشیاء generator استفاده از توابع generator است.

بیایید به چند مثال نگاه کنیم.

#### مثال 1

اینجا یک مثال بسیار ساده از یک تابع generator است

```{code-cell} python3
def f():
    yield 'start'
    yield 'middle'
    yield 'end'
```

شبیه یک تابع به نظر می‌رسد، اما از کلمه کلیدی `yield` استفاده می‌کند که قبلاً با آن آشنا نشده‌ایم.

بیایید ببینیم بعد از اجرای این کد چگونه کار می‌کند

```{code-cell} python3
type(f)
```

```{code-cell} python3
gen = f()
gen
```

```{code-cell} python3
next(gen)
```

```{code-cell} python3
next(gen)
```

```{code-cell} python3
next(gen)
```

```{code-cell} python3
---
tags: [raises-exception]
---
next(gen)
```

تابع generator `f()` برای ایجاد اشیاء generator استفاده می‌شود (در این مورد `gen`).

Generatorها iterator هستند، زیرا از متد `next` پشتیبانی می‌کنند.

اولین فراخوانی `next(gen)`

* کد در بدنه `f()` را تا زمانی که با دستور `yield` مواجه شود، اجرا می‌کند.
* آن مقدار را به فراخواننده `next(gen)` برمی‌گرداند.

فراخوانی دوم `next(gen)` از *خط بعدی* شروع به اجرا می‌کند

```{code-cell} python3
def f():
    yield 'start'
    yield 'middle'  # This line!
    yield 'end'
```

و تا دستور `yield` بعدی ادامه می‌دهد.

در آن نقطه مقدار بعد از `yield` را به فراخواننده `next(gen)` برمی‌گرداند، و غیره.

وقتی بلوک کد تمام می‌شود، generator یک خطای `StopIteration` پرتاب می‌کند.

#### مثال 2

مثال بعدی ما یک آرگومان `x` را از فراخواننده دریافت می‌کند

```{code-cell} python3
def g(x):
    while x < 100:
        yield x
        x = x * x
```

بیایید ببینیم چگونه کار می‌کند

```{code-cell} python3
g
```

```{code-cell} python3
gen = g(2)
type(gen)
```

```{code-cell} python3
next(gen)
```

```{code-cell} python3
next(gen)
```

```{code-cell} python3
next(gen)
```

```{code-cell} python3
---
tags: [raises-exception]
---
next(gen)
```

فراخوانی `gen = g(2)` ، `gen` را به یک generator متصل می‌کند.

در داخل generator، نام `x` به `2` متصل است.

وقتی `next(gen)` را فراخوانی می‌کنیم

* بدنه `g()` تا خط `yield x` اجرا می‌شود و مقدار `x` برگردانده می‌شود.

توجه کنید که مقدار `x` در داخل generator حفظ می‌شود.

وقتی دوباره `next(gen)` را فراخوانی می‌کنیم، اجرا *از جایی که متوقف شده بود* ادامه می‌یابد

```{code-cell} python3
def g(x):
    while x < 100:
        yield x
        x = x * x  # execution continues from here
```

وقتی `x < 100` شکست می‌خورد، generator یک خطای `StopIteration` پرتاب می‌کند.

اتفاقاً، حلقه داخل generator می‌تواند بی‌نهایت باشد

```{code-cell} python3
def g(x):
    while 1:
        yield x
        x = x * x
```

### مزایای Iteratorها

مزیت استفاده از iterator اینجا چیست؟

فرض کنید می‌خواهیم از یک binomial(n,0.5) نمونه بگیریم.

یک راه برای انجام آن به شرح زیر است

```{code-cell} python3
import random
n = 10000000
draws = [random.uniform(0, 1) < 0.5 for i in range(n)]
sum(draws)
```

اما ما در حال ایجاد دو لیست بزرگ هستیم، `range(n)` و `draws`.

این از حافظه زیادی استفاده می‌کند و بسیار کند است.

اگر `n` را حتی بزرگ‌تر کنیم، این اتفاق می‌افتد

```{code-cell} python3
---
tags: [raises-exception]
---
n = 100000000
draws = [random.uniform(0, 1) < 0.5 for i in range(n)]
```

می‌توانیم با استفاده از iteratorها از این مشکلات اجتناب کنیم.

اینجا تابع generator است

```{code-cell} python3
def f(n):
    i = 1
    while i <= n:
        yield random.uniform(0, 1) < 0.5
        i += 1
```

حالا بیایید جمع را انجام دهیم

```{code-cell} python3
n = 10000000
draws = f(n)
draws
```

```{code-cell} python3
sum(draws)
```

خلاصه، iterableها

* نیاز به ایجاد لیست‌ها/tupleهای بزرگ را از بین می‌برند، و
* یک رابط یکنواخت برای تکرار فراهم می‌کنند که می‌تواند به صورت شفاف در حلقه‌های `for` استفاده شود

## تمرین‌ها


```{exercise-start}
:label: paf_ex1
```

کد زیر را کامل کنید و آن را با استفاده از [این فایل csv](https://raw.githubusercontent.com/QuantEcon/lecture-python-programming/main/lectures/_static/lecture_specific/python_advanced_features/test_table.csv) تست کنید، که فرض می‌کنیم آن را در دایرکتوری کاری فعلی خود قرار داده‌اید

```{code-block} python3
:class: no-execute

def column_iterator(target_file, column_number):
    """A generator function for CSV files.
    When called with a file name target_file (string) and column number
    column_number (integer), the generator function returns a generator
    that steps through the elements of column column_number in file
    target_file.
    """
    # put your code here

dates = column_iterator('test_table.csv', 1)

for date in dates:
    print(date)
```

```{exercise-end}
```

```{solution-start} paf_ex1
:class: dropdown
```

یک راه‌حل به شرح زیر است

```{code-cell} python3
def column_iterator(target_file, column_number):
    """A generator function for CSV files.
    When called with a file name target_file (string) and column number
    column_number (integer), the generator function returns a generator
    which steps through the elements of column column_number in file
    target_file.
    """
    f = open(target_file, 'r')
    for line in f:
        yield line.split(',')[column_number - 1]
    f.close()

dates = column_iterator('test_table.csv', 1)

i = 1
for date in dates:
    print(date)
    if i == 10:
        break
    i += 1
```

```{solution-end}
```
