---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.16.7
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
translation:
  title: Numba
  headings:
    Overview: مروری کلی
    Compiling Functions: کامپایل کردن توابع
    Compiling Functions::An Example: یک مثال
    Compiling Functions::How and When it Works: چگونه و چه زمانی کار می‌کند
    Decorator Notation: نماد Decorator
    Type Inference: استنتاج نوع
    Dangers and Limitations: خطرات و محدودیت‌ها
    Dangers and Limitations::Limitations: محدودیت‌ها
    'Dangers and Limitations::A Gotcha: Global Variables': 'یک مشکل: متغیرهای سراسری'
    Multithreaded Loops in Numba: حلقه‌های چندنخی در Numba
    Exercises: تمرین‌ها
---

(speed)=
```{raw} jupyter
<div id="qe-notebook-header" align="right" style="text-align:right;">
        <a href="https://quantecon.org/" title="quantecon.org">
                <img style="width:250px;display:inline;" width="250px" src="https://assets.quantecon.org/img/qe-menubar-logo.svg" alt="QuantEcon">
        </a>
</div>
```

# Numba

علاوه بر آنچه در Anaconda موجود است، این درس به کتابخانه‌های زیر نیاز دارد:

```{code-cell} ipython3
:tags: [hide-output]

!pip install quantecon
```

لطفاً اطمینان حاصل کنید که آخرین نسخه Anaconda را دارید، زیرا نسخه‌های قدیمی یک {doc}`منبع رایج خطا <troubleshooting>` هستند.

بیایید با چند import شروع کنیم:

```{code-cell} ipython3
import numpy as np
import quantecon as qe
import matplotlib.pyplot as plt
```

## مروری کلی

در یک {doc}`درس قبلی <need_for_speed>` درباره برداری‌سازی بحث کردیم،
که می‌تواند سرعت اجرا را با ارسال دسته‌ای عملیات پردازش آرایه به کد کارآمد سطح پایین بهبود بخشد.

با این حال، همانطور که {ref}`در آن درس بحث شد <numba-p_c_vectorization>`،
طرح‌های سنتی برداری‌سازی، مانند آنچه در MATLAB، Julia و NumPy یافت می‌شود، نقاط ضعفی دارند.

* برای عملیات ترکیبی آرایه‌ها بسیار حافظه‌بر هستند
* برای برخی الگوریتم‌ها ناکارآمد یا غیرممکن است.

یک راه برای دور زدن این مشکلات، استفاده از [Numba](https://numba.pydata.org/) است، یک
**کامپایلر در زمان اجرا (JIT)** برای Python که به سمت کار عددی جهت‌گیری شده است.

Numba توابع را در حین اجرا به دستورالعمل‌های کد ماشین بومی کامپایل می‌کند.

وقتی موفق می‌شود، Numba با کد ماشین زبان‌های سطح پایین برابری خواهد کرد.

علاوه بر این، Numba می‌تواند ترفندهای مفید دیگری نیز انجام دهد، مانند {ref}`چندنخی <multithreading>` یا
ارتباط با GPU‌ها (از طریق `numba.cuda`).

کامپایلر JIT نامبا از بسیاری جهات مشابه کامپایلر JIT در JULIA است.

تفاوت اصلی در این است که کمتر جاه‌طلبانه است و تلاش می‌کند زیرمجموعه کوچک‌تری از زبان را کامپایل کند.

اگرچه این ممکن است مانند یک نقص به نظر برسد، اما از برخی جهات یک مزیت محسوب می‌شود.

Numba سبک، آسان برای استفاده و بسیار خوب در آنچه انجام می‌دهد است.

این درس ایده‌های اصلی را معرفی می‌کند.

(numba_link)=
## {index}`کامپایل کردن توابع <single: Compiling Functions>`

```{index} single: Python; Numba
```


(quad_map_eg)=
### یک مثال

بیایید مسئله‌ای را در نظر بگیریم که برداری‌سازی آن دشوار است (یعنی واگذاری آن به عملیات پردازش آرایه).

این مسئله شامل تولید مسیر از طریق نگاشت درجه دوم است:

$$
    x_{t+1} = \alpha x_t (1 - x_t)
$$

در ادامه مقدار زیر را تنظیم می‌کنیم:

```{code-cell} ipython3
α = 4.0
```

در اینجا نمودار یک مسیر معمول را مشاهده می‌کنید که از $x_0 = 0.1$ شروع می‌شود و $t$ روی محور x قرار دارد:

```{code-cell} ipython3
def qm(x0, n):
    x = np.empty(n+1)
    x[0] = x0
    for t in range(n):
      x[t+1] = α * x[t] * (1 - x[t])
    return x

x = qm(0.1, 250)
fig, ax = plt.subplots()
ax.plot(x, 'b-', lw=2, alpha=0.8)
ax.set_xlabel('$t$', fontsize=12)
ax.set_ylabel('$x_{t}$', fontsize = 12)
plt.show()
```

برای تسریع تابع `qm` با استفاده از Numba، اولین گام ما این است:

```{code-cell} ipython3
from numba import jit

qm_numba = jit(qm)
```

تابع `qm_numba` نسخه‌ای از `qm` است که برای کامپایل JIT «هدف‌گذاری» شده است.

به زودی توضیح خواهیم داد که این به چه معناست.

بیایید فراخوانی‌های یکسان تابع را در این دو نسخه زمان‌بندی و مقایسه کنیم، ابتدا با تابع اصلی `qm`:

```{code-cell} ipython3
n = 10_000_000

with qe.Timer() as timer1:
    qm(0.1, int(n))
time1 = timer1.elapsed
```

حالا بیایید qm_numba را امتحان کنیم:

```{code-cell} ipython3
with qe.Timer() as timer2:
    qm_numba(0.1, int(n))
time2 = timer2.elapsed
```

این از قبل یک افزایش سرعت بسیار زیاد است.

در واقع، دفعه بعد و تمام دفعات بعدی حتی سریع‌تر اجرا می‌شود زیرا تابع کامپایل شده و در حافظه قرار دارد:

(qm_numba_result)=

```{code-cell} ipython3
with qe.Timer() as timer3:
    qm_numba(0.1, int(n))
time3 = timer3.elapsed
```

```{code-cell} ipython3
time1 / time3  # Calculate speed gain
```

### چگونه و چه زمانی کار می‌کند

Numba تلاش می‌کند با استفاده از زیرساختی که [پروژه LLVM](https://llvm.org/) فراهم کرده، کد ماشین سریع تولید کند.

این کار را با استنتاج اطلاعات نوع به صورت لحظه‌ای انجام می‌دهد.

(برای بحث درباره انواع، {doc}`درس قبلی <need_for_speed>` ما درباره محاسبات علمی را ببینید.)

ایده اصلی این است:

* پایتون بسیار انعطاف‌پذیر است و از این رو می‌توانیم تابع qm را با انواع مختلفی فراخوانی کنیم.
    * مثلاً `x0` می‌تواند یک آرایه NumPy یا یک لیست باشد، `n` می‌تواند یک عدد صحیح یا یک عدد اعشاری باشد و غیره.
* این امر تولید کد ماشین کارآمد *پیش از زمان اجرا* را بسیار دشوار می‌سازد.
* اما وقتی واقعاً تابع را *فراخوانی* می‌کنیم، مثلاً با اجرای `qm(0.5, 10)`، انواع `x0` و `n` مشخص می‌شوند.
* علاوه بر این، انواع *سایر متغیرها* در `qm` *می‌توانند پس از مشخص شدن انواع ورودی استنتاج شوند*.
* بنابراین استراتژی Numba و سایر کامپایلرهای JIT این است که *تا زمان فراخوانی تابع صبر کنند* و سپس کامپایل کنند.

به همین دلیل است که به آن کامپایل «درست به موقع» (just-in-time) گفته می‌شود.

توجه داشته باشید که اگر `qm(0.5, 10)` را فراخوانی کنید و سپس `qm(0.9, 20)` را دنبال آن بیاورید، کامپایل تنها در اولین فراخوانی انجام می‌شود.

این به این دلیل است که کد کامپایل‌شده در حافظه نهان ذخیره می‌شود و در صورت نیاز مجدداً استفاده می‌شود.

به همین دلیل است که در کد بالا، `time3` کوچک‌تر از `time2` است.

## نماد Decorator

در کد بالا، نسخه کامپایل شده JIT از `qm` را از طریق فراخوانی ایجاد کردیم

```{code-cell} ipython3
qm_numba = jit(qm)
```

در عمل، این معمولاً با استفاده از یک سینتکس جایگزین *decorator* انجام می‌شود.

(ما decoratorها را در یک {doc}`درس جداگانه <python_advanced_features>` بحث می‌کنیم اما می‌توانید جزئیات را در این مرحله رد کنید.)

به طور مشخص، برای هدف‌گیری یک تابع برای کامپایل JIT، می‌توانیم `@jit` را قبل از تعریف تابع قرار دهیم.

در اینجا نحوه انجام این کار برای `qm` آمده است

```{code-cell} ipython3
@jit
def qm(x0, n):
    x = np.empty(n+1)
    x[0] = x0
    for t in range(n):
        x[t+1] = α * x[t] * (1 - x[t])
    return x
```

این معادل افزودن `qm = jit(qm)` بعد از تعریف تابع است.

موارد زیر اکنون از نسخه jitted استفاده می‌کنند:

```{code-cell} ipython3
with qe.Timer(precision=4):
    qm(0.1, 100_000)
```

```{code-cell} ipython3
with qe.Timer(precision=4):
    qm(0.1, 100_000)
```

Numba همچنین چندین آرگومان برای decoratorها جهت تسریع محاسبات و ذخیره‌سازی cache توابع ارائه می‌دهد -- [اینجا](https://numba.readthedocs.io/en/stable/user/performance-tips.html) را ببینید.

## استنتاج نوع

استنتاج موفقیت‌آمیز نوع، بخش کلیدی کامپایل JIT است.

همانطور که می‌توانید تصور کنید، استنتاج انواع برای اشیاء ساده Python (مثلاً انواع داده اسکالر ساده مانند اعشاری و صحیح) آسان‌تر است.

Numba همچنین با آرایه‌های NumPy که انواع به خوبی تعریف شده دارند، به خوبی کار می‌کند.

در یک محیط ایده‌آل، Numba می‌تواند تمام اطلاعات نوع لازم را استنتاج کند.

این به آن اجازه می‌دهد تا کد ماشین بومی کارآمدی تولید کند، بدون نیاز به فراخوانی محیط زمان اجرای Python.

وقتی Numba نمی‌تواند تمام اطلاعات نوع را استنتاج کند، خطا ایجاد می‌کند.

به عنوان مثال، در تنظیم (مصنوعی) زیر، Numba قادر به تعیین نوع تابع `mean` هنگام کامپایل تابع `bootstrap` نیست

```{code-cell} ipython3
@jit
def bootstrap(data, statistics, n_resamples):
    bootstrap_stat = np.empty(n_resamples)
    n = len(data)
    for i in range(n_resamples):
        resample = np.random.choice(data, size=n, replace=True)
        bootstrap_stat[i] = statistics(resample)
    return bootstrap_stat

# هیچ decorator اینجا نیست.
def mean(data):
    return np.mean(data)

data = np.array((2.3, 3.1, 4.3, 5.9, 2.1, 3.8, 2.2))
n_resamples = 10

# این کد خطا ایجاد می‌کند
try:
    bootstrap(data, mean, n_resamples)
except Exception as e:
    print(e)
```

می‌توانیم این خطا را در این مورد به راحتی با کامپایل کردن `mean` برطرف کنیم.

```{code-cell} ipython3
@jit
def mean(data):
    return np.mean(data)

with qe.Timer():
    bootstrap(data, mean, n_resamples)
```

## خطرات و محدودیت‌ها

بیایید برخی یادداشت‌های احتیاطی اضافه کنیم.

### محدودیت‌ها

همانطور که دیدیم، Numba باید اطلاعات نوع را روی تمام متغیرها استنتاج کند تا دستورالعمل‌های سریع سطح ماشین تولید کند.

برای روال‌های بزرگ یا روال‌هایی که از کتابخانه‌های خارجی استفاده می‌کنند، این فرایند به راحتی می‌تواند شکست بخورد.

از این رو، بهتر است روی تسریع قطعات کوچک و حیاتی کد تمرکز کنید.

این به شما عملکرد بسیار بهتری نسبت به پوشاندن برنامه‌های Python خود با عبارت‌های `@jit` می‌دهد.

### یک مشکل: متغیرهای سراسری

چیز دیگری که هنگام استفاده از Numba باید مراقب آن باشید در اینجا است.

مثال زیر را در نظر بگیرید

```{code-cell} ipython3
a = 1

@jit
def add_a(x):
    return a + x

print(add_a(10))
```

```{code-cell} ipython3
a = 2

print(add_a(10))
```

توجه داشته باشید که تغییر global هیچ تأثیری بر مقدار برگشتی توسط تابع نداشت.

وقتی Numba کد ماشین را برای توابع کامپایل می‌کند، متغیرهای سراسری را به عنوان ثابت برای اطمینان از پایداری نوع درمان می‌کند.

(multithreading)=
## حلقه‌های چندنخی در Numba

علاوه بر کامپایل JIT، Numba پشتیبانی از محاسبات موازی در CPUها و GPUها ارائه می‌دهد.

ابزار کلیدی برای موازی‌سازی در CPUها در Numba تابع `prange` است که به Numba می‌گوید تا تکرارهای حلقه را به صورت موازی در هسته‌های موجود اجرا کند.

برای نمایش، ابتدا به یک قطعه کد ساده تک‌نخی (یعنی غیرموازی) نگاه می‌کنیم.

کد، به‌روزرسانی ثروت $w_t$ یک خانوار را از طریق قانون شبیه‌سازی می‌کند

$$
w_{t+1} = R_{t+1} s w_t + y_{t+1}
$$

در اینجا

* $R$ نرخ بازده ناخالص دارایی‌ها است
* $s$ نرخ پس‌انداز خانوار است و
* $y$ درآمد کار است.

ما هر دوی $R$ و $y$ را به عنوان کشش‌های مستقل از یک توزیع لگ‌نرمال مدل‌سازی می‌کنیم.

در اینجا کد است:

```{code-cell} ipython3
from numba import jit

@jit
def h(w, r=0.1, s=0.3, v1=0.1, v2=1.0):
    """
    ثروت خانوار را به‌روزرسانی می‌کند.
    """

    # شوک‌ها را بکش
    R = np.exp(v1 * np.random.randn()) * (1 + r)
    y = np.exp(v2 * np.random.randn())

    # ثروت را به‌روزرسانی کن
    w = R * s * w + y
    return w
```

بیایید نگاهی بیندازیم که چگونه ثروت تحت این قانون تکامل می‌یابد.

```{code-cell} ipython3
fig, ax = plt.subplots()

T = 100
w = np.empty(T)
w[0] = 5
for t in range(T-1):
    w[t+1] = h(w[t])

ax.plot(w)
ax.set_xlabel('$t$', fontsize=12)
ax.set_ylabel('$w_{t}$', fontsize=12)
plt.show()
```

حالا فرض کنیم که جمعیت زیادی از خانوارها داریم و می‌خواهیم بدانیم میانه ثروت چه خواهد بود.

حل این موضوع با مداد و کاغذ آسان نیست، بنابراین به جای آن از شبیه‌سازی استفاده خواهیم کرد:

1. تعداد زیادی از خانوارها را در طول زمان شبیه‌سازی می‌کنیم
2. میانه ثروت را محاسبه می‌کنیم

در اینجا کد است:

```{code-cell} ipython3
@jit
def compute_long_run_median(w0=1, T=1000, num_reps=50_000):

    obs = np.empty(num_reps)
    for i in range(num_reps):
        w = w0
        for t in range(T):
            w = h(w)
        obs[i] = w

    return np.median(obs)
```

بیایید ببینیم چقدر سریع اجرا می‌شود:

```{code-cell} ipython3
with qe.Timer():
    compute_long_run_median()
```

برای تسریع این، آن را از طریق چندنخی موازی‌سازی خواهیم کرد.

برای این کار، پرچم `parallel=True` را اضافه کرده و `range` را به `prange` تغییر می‌دهیم:

```{code-cell} ipython3
from numba import prange

@jit(parallel=True)
def compute_long_run_median_parallel(w0=1, T=1000, num_reps=50_000):

    obs = np.empty(num_reps)
    for i in prange(num_reps):
        w = w0
        for t in range(T):
            w = h(w)
        obs[i] = w

    return np.median(obs)
```

بیایید به زمان‌بندی نگاه کنیم:

```{code-cell} ipython3
with qe.Timer():
    compute_long_run_median_parallel()
```

افزایش سرعت قابل توجه است.

توجه داشته باشید که موازی‌سازی را در سطح خانوارها انجام می‌دهیم نه در طول زمان -- به‌روزرسانی‌های یک خانوار در دوره‌های زمانی مختلف ذاتاً ترتیبی هستند.

## تمرین‌ها

```{exercise}
:label: speed_ex1

{ref}`قبلاً <pbe_ex5>` در نظر گرفتیم که چگونه $\pi$ را با Monte Carlo تقریب بزنیم.

از همان ایده اینجا استفاده کنید، اما کد را با استفاده از Numba کارآمد کنید.

سرعت را با و بدون Numba هنگامی که اندازه نمونه بزرگ است مقایسه کنید.
```

```{solution-start} speed_ex1
:class: dropdown
```

در اینجا یک راه‌حل است:

```{code-cell} ipython3
@jit
def calculate_pi(n=1_000_000):
    count = 0
    for i in range(n):
        u, v = np.random.uniform(0, 1), np.random.uniform(0, 1)
        d = np.sqrt((u - 0.5)**2 + (v - 0.5)**2)
        if d < 0.5:
            count += 1

    area_estimate = count / n
    return area_estimate * 4  # تقسیم بر radius**2
```

حالا بیایید ببینیم چقدر سریع اجرا می‌شود:

```{code-cell} ipython3
with qe.Timer():
    calculate_pi()
```

```{code-cell} ipython3
with qe.Timer():
    calculate_pi()
```

اگر کامپایل JIT را با حذف `@jit` خاموش کنیم، کد حدود 150 برابر بیشتر در دستگاه ما طول می‌کشد.

بنابراین با افزودن چهار کاراکتر، افزایش سرعت 2 مرتبه بزرگی به دست می‌آوریم.

```{solution-end}
```

```{exercise-start}
:label: speed_ex2
```

در سری درس‌های [مقدمه‌ای بر اقتصاد کمی با
Python](https://intro.quantecon.org/intro.html) می‌توانید همه چیز درباره زنجیره‌های مارکوف حالت محدود یاد بگیرید.

فعلاً، فقط روی شبیه‌سازی یک مثال بسیار ساده از چنین زنجیره‌ای تمرکز کنیم.

فرض کنید که نوسان بازده یک دارایی می‌تواند در یکی از دو رژیم باشد -- بالا یا پایین.

احتمالات انتقال در بین حالت‌ها به شرح زیر است

```{image} /_static/lecture_specific/sci_libs/nfs_ex1.png
:align: center
```

به عنوان مثال، فرض کنید طول دوره یک روز است و فرض کنید حالت فعلی بالا است.

از نمودار می‌بینیم که حالت فردا خواهد بود

* بالا با احتمال 0.8
* پایین با احتمال 0.2

وظیفه شما شبیه‌سازی یک دنباله از حالت‌های نوسان روزانه طبق این قانون است.

طول دنباله را `n = 1_000_000` تنظیم کنید و در حالت بالا شروع کنید.

یک نسخه Python خالص و یک نسخه Numba پیاده‌سازی کنید و سرعت‌ها را مقایسه کنید.

برای آزمایش کد خود، کسری از زمان که زنجیر در حالت پایین می‌گذراند را ارزیابی کنید.

اگر کد شما صحیح باشد، باید حدود 2/3 باشد.


```{hint}
:class: dropdown

* حالت پایین را به عنوان 0 و حالت بالا را به عنوان 1 نمایش دهید.
* اگر می‌خواهید اعداد صحیح را در یک آرایه NumPy ذخیره کنید و سپس کامپایل JIT اعمال کنید، از `x = np.empty(n, dtype=np.int64)` استفاده کنید.

```

```{exercise-end}
```

```{solution-start} speed_ex2
:class: dropdown
```

ما قرار می‌دهیم

- 0 نشان‌دهنده "پایین"
- 1 نشان‌دهنده "بالا"

```{code-cell} ipython3
p, q = 0.1, 0.2  # احتمال خروج از حالت پایین و بالا به ترتیب
```

در اینجا نسخه Python خالص تابع است

```{code-cell} ipython3
def compute_series(n):
    x = np.empty(n, dtype=np.int64)
    x[0] = 1  # در حالت 1 شروع کن
    U = np.random.uniform(0, 1, size=n)
    for t in range(1, n):
        current_x = x[t-1]
        if current_x == 0:
            x[t] = U[t] < p
        else:
            x[t] = U[t] > q
    return x
```

بیایید این کد را اجرا کنیم و بررسی کنیم که کسری از زمان صرف شده در حالت پایین حدود 0.666 است

```{code-cell} ipython3
n = 1_000_000
x = compute_series(n)
print(np.mean(x == 0))  # کسری از زمان که x در حالت 0 است
```

این (تقریباً) خروجی صحیح است.

حالا بیایید زمان آن را بگیریم:

```{code-cell} ipython3
with qe.Timer():
    compute_series(n)
```

بعد بیایید یک نسخه Numba پیاده‌سازی کنیم که آسان است

```{code-cell} ipython3
compute_series_numba = jit(compute_series)
```

بیایید بررسی کنیم که هنوز اعداد صحیح دریافت می‌کنیم

```{code-cell} ipython3
x = compute_series_numba(n)
print(np.mean(x == 0))
```

بیایید زمان را ببینیم

```{code-cell} ipython3
with qe.Timer():
    compute_series_numba(n)
```

این بهبود سرعت خوبی برای یک خط کد است!

```{solution-end}
```

```{exercise}
:label: numba_ex3

در {ref}`یک تمرین قبلی <speed_ex1>`، از Numba برای تسریع تلاشی برای محاسبه ثابت $\pi$ با Monte Carlo استفاده کردیم.

اکنون سعی کنید موازی‌سازی را اضافه کنید و ببینید آیا افزایش سرعت بیشتری به دست می‌آورید.

نباید انتظار افزایش بزرگی در اینجا داشته باشید زیرا، در حالی که وظایف مستقل زیادی وجود دارد (کشیدن نقطه و آزمایش اگر در دایره است)، هر کدام زمان اجرای کمی دارد.

به طور کلی، موازی‌سازی زمانی کمتر موثر است که وظایف فردی که باید موازی شوند نسبت به کل زمان اجرا بسیار کوچک باشند.

این به دلیل سربارهای مرتبط با توزیع همه این وظایف کوچک در چندین CPU است.

با این وجود، با سخت‌افزار مناسب، امکان به دست آوردن افزایش سرعت غیر بدیهی در این تمرین وجود دارد.

برای اندازه شبیه‌سازی Monte Carlo، از چیزی قابل توجه استفاده کنید، مانند `n = 100_000_000`.
```

```{solution-start} numba_ex3
:class: dropdown
```

در اینجا یک راه‌حل است:

```{code-cell} ipython3
@jit(parallel=True)
def calculate_pi(n=1_000_000):
    count = 0
    for i in prange(n):
        u, v = np.random.uniform(0, 1), np.random.uniform(0, 1)
        d = np.sqrt((u - 0.5)**2 + (v - 0.5)**2)
        if d < 0.5:
            count += 1

    area_estimate = count / n
    return area_estimate * 4  # تقسیم بر radius**2
```

حالا بیایید ببینیم چقدر سریع اجرا می‌شود:

```{code-cell} ipython3
with qe.Timer():
    calculate_pi()
```

```{code-cell} ipython3
with qe.Timer():
    calculate_pi()
```

با روشن و خاموش کردن موازی‌سازی (انتخاب `True` یا `False` در annotation `@jit`)، می‌توانیم افزایش سرعتی که چندنخی علاوه بر کامپایل JIT فراهم می‌کند را آزمایش کنیم.

در ایستگاه کاری ما، می‌بینیم که موازی‌سازی سرعت اجرا را با ضریب 2 یا 3 افزایش می‌دهد.

(اگر به صورت محلی اجرا می‌کنید، اعداد متفاوتی خواهید گرفت که عمدتاً به تعداد CPUها در دستگاه شما بستگی دارد.)

```{solution-end}
```


```{exercise}
:label: numba_ex4

در {doc}`درس ما درباره SciPy<scipy>`، قیمت‌گذاری یک اختیار خرید را در تنظیمی که قیمت سهام پایه یک توزیع ساده و شناخته شده داشت بحث کردیم.

در اینجا یک تنظیم واقعی‌تر را بحث می‌کنیم.

یادآوری می‌کنیم که قیمت اختیار از قانون زیر پیروی می‌کند

$$
P = \beta^n \mathbb E \max\{ S_n - K, 0 \}
$$

که در آن

1. $\beta$ یک ضریب تنزیل است،
2. $n$ تاریخ انقضا است،
2. $K$ قیمت اعمال است و
3. $\{S_t\}$ قیمت دارایی پایه در هر زمان $t$ است.

فرض کنید که `n, β, K = 20, 0.99, 100`.

فرض کنید که قیمت سهام از قانون زیر پیروی می‌کند

$$
\ln \frac{S_{t+1}}{S_t} = \mu + \sigma_t \xi_{t+1}
$$

که در آن

$$
    \sigma_t = \exp(h_t),
    \quad
        h_{t+1} = \rho h_t + \nu \eta_{t+1}
$$

در اینجا $\{\xi_t\}$ و $\{\eta_t\}$ مستقل و هم‌توزیع و نرمال استاندارد هستند.

(این یک مدل **نوسان تصادفی** است، که در آن نوسان $\sigma_t$ در طول زمان تغییر می‌کند.)

از مقادیر پیش‌فرض `μ, ρ, ν, S0, h0 = 0.0001, 0.1, 0.001, 10, 0` استفاده کنید.

(در اینجا `S0` همان $S_0$ و `h0` همان $h_0$ است.)

با تولید $M$ مسیر $s_0, \ldots, s_n$، تخمین مونت‌کارلو را محاسبه کنید

$$
    \hat P_M
    := \beta^n \mathbb E \max\{ S_n - K, 0 \}
    \approx
    \frac{1}{M} \sum_{m=1}^M \max \{S_n^m - K, 0 \}
$$


از قیمت، با اعمال Numba و موازی‌سازی.

```


```{solution-start} numba_ex4
:class: dropdown
```


با $s_t := \ln S_t$، پویایی قیمت به شکل زیر می‌شود

$$
s_{t+1} = s_t + \mu + \exp(h_t) \xi_{t+1}
$$

با استفاده از این واقعیت، راه‌حل را می‌توان به شرح زیر نوشت.


```{code-cell} ipython3
M = 10_000_000

n, β, K = 20, 0.99, 100
μ, ρ, ν, S0, h0 = 0.0001, 0.1, 0.001, 10, 0

@jit(parallel=True)
def compute_call_price_parallel(β=β,
                                μ=μ,
                                S0=S0,
                                h0=h0,
                                K=K,
                                n=n,
                                ρ=ρ,
                                ν=ν,
                                M=M):
    current_sum = 0.0
    # برای هر مسیر نمونه
    for m in prange(M):
        s = np.log(S0)
        h = h0
        # شبیه‌سازی رو به جلو در زمان
        for t in range(n):
            s = s + μ + np.exp(h) * np.random.randn()
            h = ρ * h + ν * np.random.randn()
        # و مقدار max{S_n - K, 0} را به current_sum اضافه کن
        current_sum += max(np.exp(s) - K, 0)

    return β**n * current_sum / M
```

سعی کنید بین `parallel=True` و `parallel=False` جابجا شوید و زمان اجرا را یادداشت کنید.

اگر روی دستگاهی با CPUهای زیاد هستید، تفاوت باید قابل توجه باشد.

```{solution-end}
```
