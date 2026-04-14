---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.17.2
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
translation:
  title: JAX
  headings:
    JAX as a NumPy Replacement: JAX به عنوان جایگزین NumPy
    JAX as a NumPy Replacement::Similarities: شباهت‌ها
    JAX as a NumPy Replacement::Differences: تفاوت‌ها
    JAX as a NumPy Replacement::Differences::Speed!: سرعت!
    JAX as a NumPy Replacement::Differences::Speed!::With NumPy: با NumPy
    JAX as a NumPy Replacement::Differences::Speed!::With JAX: با JAX
    JAX as a NumPy Replacement::Differences::Size Experiment: آزمایش اندازه
    JAX as a NumPy Replacement::Differences::Precision: دقت
    JAX as a NumPy Replacement::Differences::Immutability: تغییرناپذیری
    JAX as a NumPy Replacement::Differences::A Workaround: راه‌حل جایگزین
    Functional Programming: برنامه‌نویسی تابعی
    Functional Programming::Pure functions: توابع خالص
    Functional Programming::Examples -- Pure and Impure: مثال‌ها -- توابع خالص و ناخالص
    Functional Programming::Why Functional Programming?: چرا برنامه‌نویسی تابعی؟
    Random numbers: اعداد تصادفی
    Random numbers::NumPy / MATLAB Approach: رویکرد NumPy / MATLAB
    Random numbers::JAX: JAX
    Random numbers::Benefits: مزایا
    JIT Compilation: کامپایل JIT
    JIT Compilation::With NumPy: با NumPy
    JIT Compilation::With JAX: با JAX
    JIT Compilation::Compiling the Whole Function: کامپایل کل تابع
    JIT Compilation::How JIT compilation works: نحوه کار کامپایل JIT
    JIT Compilation::Compiling non-pure functions: کامپایل توابع غیرخالص
    Vectorization with `vmap`: برداری‌سازی با `vmap`
    Vectorization with `vmap`::A simple example: یک مثال ساده
    Vectorization with `vmap`::Combining transformations: ترکیب تبدیل‌ها
    'Automatic differentiation: a preview': 'مشتق‌گیری خودکار: یک پیش‌نمایش'
    Exercises: تمرین‌ها
---

# JAX

این سخنرانی مقدمه‌ای کوتاه بر [Google JAX](https://github.com/jax-ml/jax) ارائه می‌دهد.

```{include} _admonition/gpu.md
```

JAX یک کتابخانه محاسبات علمی با کارایی بالا است که موارد زیر را فراهم می‌کند:

* یک رابط شبیه [NumPy](https://en.wikipedia.org/wiki/NumPy) که می‌تواند به صورت خودکار در CPUها و GPUها موازی‌سازی شود،
* یک کامپایلر just-in-time برای تسریع طیف گسترده‌ای از عملیات عددی، و
* [تمایز خودکار](https://en.wikipedia.org/wiki/Automatic_differentiation).

به طور فزاینده‌ای، JAX همچنین [روتین‌های محاسبات علمی تخصصی‌تری](https://docs.jax.dev/en/latest/jax.scipy.html) را حفظ و ارائه می‌دهد، مانند آنهایی که در ابتدا در [SciPy](https://en.wikipedia.org/wiki/SciPy) یافت می‌شدند.

علاوه بر آنچه در Anaconda موجود است، این سخنرانی به کتابخانه‌های زیر نیاز دارد:

```{code-cell} ipython3
:tags: [hide-output]

!pip install jax quantecon
```

از import‌های زیر استفاده خواهیم کرد:

```{code-cell} ipython3
import jax
import jax.numpy as jnp
import matplotlib.pyplot as plt
import numpy as np
import quantecon as qe
```

## JAX به عنوان جایگزین NumPy

بیایید به شباهت‌ها و تفاوت‌های بین JAX و NumPy نگاه کنیم.

### شباهت‌ها

در بالا `jax.numpy as jnp` را وارد کردیم که یک رابط شبیه به NumPy برای عملیات آرایه فراهم می‌کند.

یکی از ویژگی‌های جذاب JAX این است که، هر زمان که امکان‌پذیر باشد، این رابط با API NumPy مطابقت دارد.

در نتیجه، اغلب می‌توانیم از JAX به عنوان جایگزین مستقیم NumPy استفاده کنیم.

در اینجا برخی عملیات استاندارد آرایه با استفاده از `jnp` آمده است:

```{code-cell} ipython3
a = jnp.asarray((1.0, 3.2, -1.5))
```

```{code-cell} ipython3
print(a)
```

```{code-cell} ipython3
print(jnp.sum(a))
```

```{code-cell} ipython3
print(jnp.dot(a, a))
```

با این حال، باید به خاطر داشت که شیء آرایه `a` یک آرایه NumPy نیست:

```{code-cell} ipython3
a
```

```{code-cell} ipython3
type(a)
```

حتی نگاشت‌های با مقدار اسکالر روی آرایه‌ها، آرایه‌های JAX را برمی‌گردانند نه اسکالرها!

```{code-cell} ipython3
jnp.sum(a)
```

### تفاوت‌ها

اکنون به برخی از تفاوت‌های بین عملیات آرایه JAX و NumPy نگاه کنیم.

(jax_speed)=
#### سرعت!

یکی از تفاوت‌های عمده این است که JAX سریع‌تر است --- و گاهی بسیار سریع‌تر.

برای نشان دادن این موضوع، فرض کنیم می‌خواهیم تابع کسینوس را در نقاط بسیاری ارزیابی کنیم.

```{code-cell}
n = 50_000_000
x = np.linspace(0, 10, n)   # NumPy array
```

##### با NumPy

بیایید با NumPy امتحان کنیم

```{code-cell}
with qe.Timer():
    # First NumPy timing
    y = np.cos(x)
```

و یک بار دیگر.

```{code-cell}
with qe.Timer():
    # Second NumPy timing
    y = np.cos(x)
```

در اینجا

* NumPy از یک باینری از پیش ساخته شده برای اعمال کسینوس بر یک آرایه از اعداد اعشاری استفاده می‌کند
* باینری روی CPU ماشین محلی اجرا می‌شود

##### با JAX

اکنون بیایید با JAX امتحان کنیم.

```{code-cell}
x = jnp.linspace(0, 10, n)
```

بیایید همان رویه را زمان‌بندی کنیم.

```{code-cell}
with qe.Timer():
    # First run
    y = jnp.cos(x)
    # Hold the interpreter until the array operation finishes
    y.block_until_ready()
```

```{note}
در بالا، متد `block_until_ready` مفسر را تا زمانی که نتایج محاسبات بازگردانده شوند نگه می‌دارد.
این برای زمان‌بندی اجرا ضروری است زیرا JAX از ارسال ناهمزمان استفاده می‌کند که
به مفسر Python اجازه می‌دهد جلوتر از محاسبات عددی حرکت کند.
```

اکنون بیایید دوباره زمان‌بندی کنیم.

```{code-cell}
with qe.Timer():
    # Second run
    y = jnp.cos(x)
    # Hold interpreter 
    y.block_until_ready()
```

روی GPU، این کد بسیار سریع‌تر از معادل NumPy خود اجرا می‌شود.

همچنین، معمولاً اجرای دوم به دلیل کامپایل JIT سریع‌تر از اجرای اول است.

این به این دلیل است که حتی توابع داخلی مانند `jnp.cos` نیز با JIT کامپایل می‌شوند --- و اجرای اول شامل زمان کامپایل است.

چرا JAX می‌خواهد توابع داخلی مانند `jnp.cos` را با JIT کامپایل کند به جای اینکه نسخه‌های از پیش کامپایل‌شده مانند NumPy ارائه دهد؟

دلیل این است که کامپایلر JIT می‌خواهد بر *اندازه* آرایه مورد استفاده (و همچنین نوع داده) تخصص پیدا کند.

اندازه برای تولید کد بهینه اهمیت دارد زیرا موازی‌سازی کارآمد نیازمند تطابق اندازه کار با سخت‌افزار موجود است.

#### آزمایش اندازه

می‌توانیم ادعا که JAX بر اندازه آرایه تخصص پیدا می‌کند را با تغییر اندازه ورودی و مشاهده زمان‌های اجرا تأیید کنیم.

```{code-cell}
x = jnp.linspace(0, 10, n + 1)
```

```{code-cell}
with qe.Timer():
    # First run
    y = jnp.cos(x)
    # Hold interpreter
    y.block_until_ready()
```

```{code-cell}
with qe.Timer():
    # Second run
    y = jnp.cos(x)
    # Hold interpreter
    y.block_until_ready()
```

زمان اجرا افزایش می‌یابد و سپس دوباره کاهش می‌یابد (این روی GPU واضح‌تر خواهد بود).

این با بحث بالا همخوانی دارد -- اولین اجرا پس از تغییر اندازه آرایه سربار کامپایل را نشان می‌دهد.

بحث بیشتر درباره کامپایل JIT در ادامه ارائه شده است.

#### دقت

یکی دیگر از تفاوت‌های بین NumPy و JAX این است که JAX به طور پیش‌فرض از اعداد اعشاری 32 بیتی استفاده می‌کند.

این به این دلیل است که JAX اغلب برای محاسبات GPU استفاده می‌شود و بیشتر محاسبات GPU از اعداد اعشاری 32 بیتی استفاده می‌کنند.

استفاده از اعداد اعشاری 32 بیتی می‌تواند منجر به افزایش سرعت قابل توجه با از دست دادن کم دقت شود.

با این حال، برای برخی محاسبات دقت مهم است.

در این موارد، اعداد اعشاری 64 بیتی را می‌توان از طریق دستور زیر اعمال کرد

```{code-cell} ipython3
jax.config.update("jax_enable_x64", True)
```

بیایید بررسی کنیم که این کار می‌کند:

```{code-cell} ipython3
jnp.ones(3)
```

#### تغییرناپذیری

به عنوان یک جایگزین NumPy، تفاوت مهم‌تر این است که آرایه‌ها به عنوان **تغییرناپذیر** در نظر گرفته می‌شوند.

برای مثال، با NumPy می‌توانیم بنویسیم

```{code-cell} ipython3
a = np.linspace(0, 1, 3)
a
```

و سپس داده‌ها را در حافظه تغییر دهیم:

```{code-cell} ipython3
a[0] = 1
a
```

در JAX این کار شکست می‌خورد 😱.

```{code-cell} ipython3
a = jnp.linspace(0, 1, 3)
a
```

```{code-cell} ipython3
try:
    a[0] = 1
except Exception as e:
    print(e)

```

طراحان JAX تصمیم گرفتند آرایه‌ها را تغییرناپذیر کنند زیرا

1. JAX از *سبک برنامه‌نویسی تابعی* استفاده می‌کند و
2. برنامه‌نویسی تابعی معمولاً از داده‌های قابل تغییر اجتناب می‌کند

این ایده‌ها را {ref}`در ادامه <jax_func>` بررسی می‌کنیم.


(jax_at_workaround)=
#### راه‌حل جایگزین

JAX یک جایگزین مستقیم برای تغییر درجای آرایه از طریق [متد `at`](https://docs.jax.dev/en/latest/_autosummary/jax.numpy.ndarray.at.html) فراهم می‌کند.

```{code-cell} ipython3
a = jnp.linspace(0, 1, 3)
```

اعمال `at[0].set(1)` یک کپی جدید از `a` را با عنصر اول تنظیم شده بر 1 برمی‌گرداند

```{code-cell} ipython3
a = a.at[0].set(1)
a
```

بدیهی است که استفاده از `at` معایبی دارد:

* نحو دست و پاگیر است و
* می‌خواهیم از ایجاد آرایه‌های جدید در حافظه هر بار که یک مقدار منفرد را تغییر می‌دهیم، اجتناب کنیم!

از این رو، در بیشتر موارد، سعی می‌کنیم از این نحو اجتناب کنیم.

(اگرچه در واقع می‌تواند داخل توابع کامپایل‌شده JIT کارآمد باشد -- اما بیایید این را فعلاً کنار بگذاریم.)


(jax_func)=
## برنامه‌نویسی تابعی

از مستندات JAX:

*هنگام پیاده‌روی در حومه ایتالیا، مردم از گفتن این که JAX دارای "una anima di pura programmazione funzionale" است، تردید نخواهند کرد.*

به عبارت دیگر، JAX یک سبک برنامه‌نویسی تابعی را فرض می‌کند.

### توابع خالص

پیامد اصلی این است که توابع JAX باید خالص باشند.

[توابع خالص](https://en.wikipedia.org/wiki/Pure_function) دارای ویژگی‌های زیر هستند:

1. *قطعی (Deterministic)*
2. *بدون عوارض جانبی*

[قطعی](https://en.wikipedia.org/wiki/Deterministic_algorithm) به این معناست که

* ورودی یکسان $\implies$ خروجی یکسان
* خروجی‌ها به وضعیت سراسری وابسته نیستند

به طور خاص، توابع خالص همیشه نتیجه یکسانی را برمی‌گردانند اگر با ورودی‌های یکسان فراخوانی شوند.

[بدون عوارض جانبی](https://en.wikipedia.org/wiki/Side_effect_(computer_science)) به این معناست که تابع

* وضعیت سراسری را تغییر نمی‌دهد
* داده‌های ارسال شده به تابع را تغییر نمی‌دهد (داده‌های تغییرناپذیر)

### مثال‌ها -- توابع خالص و ناخالص

در اینجا مثالی از یک تابع *ناخالص* آورده شده است

```{code-cell} ipython3
tax_rate = 0.1

def add_tax(prices):
    for i, price in enumerate(prices):
        prices[i] = price * (1 + tax_rate)

prices = [10.0, 20.0]
add_tax(prices)
prices
```

این تابع نمی‌تواند خالص باشد زیرا

* عوارض جانبی --- متغیر سراسری `prices` را تغییر می‌دهد
* غیرقطعی --- تغییر در متغیر سراسری `tax_rate` خروجی‌های تابع را تغییر خواهد داد، حتی با آرایه ورودی یکسان `prices`.

در اینجا یک نسخه *خالص* آورده شده است

```{code-cell} ipython3

def add_tax_pure(prices, tax_rate):
    new_prices = [price * (1 + tax_rate) for price in prices]
    return new_prices

tax_rate = 0.1
prices = (10.0, 20.0)
after_tax_prices = add_tax_pure(prices, tax_rate)
after_tax_prices
```

این نسخه خالص است زیرا

* تمام وابستگی‌ها از طریق آرگومان‌های تابع صریح هستند
* و هیچ وضعیت خارجی را تغییر نمی‌دهد

### چرا برنامه‌نویسی تابعی؟

در QuantEcon ما توابع خالص را دوست داریم زیرا

* به آزمایش کمک می‌کنند: هر تابع می‌تواند به صورت مستقل عمل کند
* رفتار قطعی و در نتیجه تکرارپذیری را ترویج می‌دهند
* از بروز اشکالاتی که از تغییر وضعیت مشترک ناشی می‌شود، جلوگیری می‌کنند

کامپایلر JAX توابع خالص و برنامه‌نویسی تابعی را دوست دارد زیرا

* وابستگی‌های داده صریح هستند، که به بهینه‌سازی محاسبات پیچیده کمک می‌کند
* توابع خالص راحت‌تر مشتق‌گیری می‌شوند (autodiff)
* توابع خالص راحت‌تر موازی‌سازی و بهینه‌سازی می‌شوند (به وضعیت تغییرپذیر مشترک وابسته نیستند)

راه دیگری برای تفکر در این مورد به شرح زیر است:

JAX توابع را به صورت گراف‌های محاسباتی نمایش می‌دهد که سپس کامپایل یا تبدیل می‌شوند (مثلاً مشتق‌گیری می‌شوند).

این گراف‌های محاسباتی توصیف می‌کنند که چگونه یک مجموعه ورودی مشخص به یک خروجی تبدیل می‌شود.

گراف‌های محاسباتی JAX ذاتاً خالص هستند.

JAX از سبک برنامه‌نویسی تابعی استفاده می‌کند تا توابع ساخته‌شده توسط کاربر مستقیماً به نمایش‌های گراف-نظری پشتیبانی‌شده توسط JAX نگاشت شوند.

## اعداد تصادفی

تولید اعداد تصادفی در JAX نسبت به الگوهای موجود در NumPy یا MATLAB بسیار متفاوت است.

### رویکرد NumPy / MATLAB

در NumPy / MATLAB، تولید با حفظ وضعیت سراسری پنهان کار می‌کند.

```{code-cell} ipython3
np.random.seed(42)
print(np.random.randn(2))   
```

هر بار که یک تابع تصادفی را فراخوانی می‌کنیم، وضعیت پنهان به‌روزرسانی می‌شود:

```{code-cell} ipython3
print(np.random.randn(2)) 
```

این تابع *خالص نیست* زیرا:

* غیرقطعی است: ورودی‌های یکسان، خروجی‌های متفاوت
* دارای عوارض جانبی است: وضعیت مولد اعداد تصادفی سراسری را تغییر می‌دهد

این در موازی‌سازی خطرناک است --- باید به دقت کنترل کرد که در هر رشته چه اتفاقی می‌افتد.

### JAX

در JAX، وضعیت مولد اعداد تصادفی به صورت صریح کنترل می‌شود.

ابتدا یک کلید تولید می‌کنیم که مولد اعداد تصادفی را seed می‌کند.

```{code-cell} ipython3
seed = 1234
key = jax.random.key(seed)
```

اکنون می‌توانیم از کلید برای تولید چند عدد تصادفی استفاده کنیم:

```{code-cell} ipython3
x = jax.random.normal(key, (3, 3))
x
```

اگر دوباره از همان کلید استفاده کنیم، در همان seed مقداردهی اولیه می‌کنیم، بنابراین اعداد تصادفی یکسان هستند:

```{code-cell} ipython3
jax.random.normal(key, (3, 3))
```

برای تولید یک نمونه (شبه) مستقل، یک گزینه "تقسیم" کلید موجود است:

```{code-cell} ipython3
key, subkey = jax.random.split(key)
```

```{code-cell} ipython3
jax.random.normal(key, (3, 3))
```

```{code-cell} ipython3
jax.random.normal(subkey, (3, 3))
```

نمودار زیر نشان می‌دهد که چگونه `split` یک درخت از کلیدها را از یک ریشه واحد تولید می‌کند، با هر کلید که نمونه‌های تصادفی مستقل تولید می‌کند.

```{code-cell} ipython3
:tags: [hide-input]

fig, ax = plt.subplots(figsize=(8, 4))
ax.set_xlim(-0.5, 6.5)
ax.set_ylim(-0.5, 3.5)
ax.set_aspect('equal')
ax.axis('off')

box_style = dict(boxstyle="round,pad=0.3", facecolor="white",
                 edgecolor="black", linewidth=1.5)
box_used = dict(boxstyle="round,pad=0.3", facecolor="#d4edda",
                edgecolor="black", linewidth=1.5)

# Root key
ax.text(3, 3, "key₀", ha='center', va='center', fontsize=11,
        bbox=box_style)

# Level 1
ax.annotate("", xy=(1.5, 2), xytext=(3, 2.7),
            arrowprops=dict(arrowstyle="->", lw=1.5))
ax.annotate("", xy=(4.5, 2), xytext=(3, 2.7),
            arrowprops=dict(arrowstyle="->", lw=1.5))
ax.text(1.5, 2, "key₁", ha='center', va='center', fontsize=11,
        bbox=box_style)
ax.text(4.5, 2, "subkey₁", ha='center', va='center', fontsize=11,
        bbox=box_used)
ax.text(5.7, 2, "→ draw", ha='left', va='center', fontsize=10,
        color='green')

# Label the split
ax.text(2, 2.65, "split", ha='center', va='center', fontsize=9,
        fontstyle='italic', color='gray')

# Level 2
ax.annotate("", xy=(0.5, 1), xytext=(1.5, 1.7),
            arrowprops=dict(arrowstyle="->", lw=1.5))
ax.annotate("", xy=(2.5, 1), xytext=(1.5, 1.7),
            arrowprops=dict(arrowstyle="->", lw=1.5))
ax.text(0.5, 1, "key₂", ha='center', va='center', fontsize=11,
        bbox=box_style)
ax.text(2.5, 1, "subkey₂", ha='center', va='center', fontsize=11,
        bbox=box_used)
ax.text(3.7, 1, "→ draw", ha='left', va='center', fontsize=10,
        color='green')

ax.text(0.7, 1.65, "split", ha='center', va='center', fontsize=9,
        fontstyle='italic', color='gray')

# Level 3
ax.annotate("", xy=(0, 0), xytext=(0.5, 0.7),
            arrowprops=dict(arrowstyle="->", lw=1.5))
ax.annotate("", xy=(1.5, 0), xytext=(0.5, 0.7),
            arrowprops=dict(arrowstyle="->", lw=1.5))
ax.text(0, 0, "key₃", ha='center', va='center', fontsize=11,
        bbox=box_style)
ax.text(1.5, 0, "subkey₃", ha='center', va='center', fontsize=11,
        bbox=box_used)
ax.text(2.7, 0, "→ draw", ha='left', va='center', fontsize=10,
        color='green')
ax.text(0, 0.65, "split", ha='center', va='center', fontsize=9,
        fontstyle='italic', color='gray')

ax.text(3, -0.5, "⋮", ha='center', va='center', fontsize=14)

ax.set_title("PRNG Key Splitting Tree", fontsize=13, pad=10)
plt.tight_layout()
plt.show()
```

این نحو برای کاربر NumPy یا Matlab غیرعادی به نظر می‌رسد --- اما وقتی به برنامه‌نویسی موازی می‌رسیم، منطقی‌تر خواهد بود.

تابع زیر `k` ماتریس تصادفی `n x n` (شبه) مستقل را با استفاده از `split` تولید می‌کند.

```{code-cell} ipython3
def gen_random_matrices(
        key,   # JAX key for random numbers
        n=2,   # Matrices will be n x n
        k=3    # Number of matrices to generate
    ):
    matrices = []
    for _ in range(k):
        key, subkey = jax.random.split(key)
        A = jax.random.uniform(subkey, (n, n))
        matrices.append(A)
    return matrices
```

```{code-cell} ipython3
seed = 42
key = jax.random.key(seed)
gen_random_matrices(key)
```

این تابع *خالص* است

* قطعی است: ورودی‌های یکسان، خروجی یکسان
* بدون عوارض جانبی: هیچ وضعیت پنهانی تغییر نمی‌کند

### مزایا

همانطور که در بالا ذکر شد، این صراحت ارزشمند است:

* تکرارپذیری: با استفاده مجدد از کلیدها، تکرار نتایج آسان است
* موازی‌سازی: کنترل آنچه در رشته‌های جداگانه اتفاق می‌افتد
* اشکال‌زدایی: نبود وضعیت پنهان، آزمایش کد را آسان‌تر می‌کند
* سازگاری با JIT: کامپایلر می‌تواند توابع خالص را به طور تهاجمی‌تری بهینه کند

## کامپایل JIT

کامپایلر just-in-time (JIT) JAX اجرا را با تولید کد ماشین کارآمد که با هم اندازه وظیفه و هم سخت‌افزار متفاوت است، تسریع می‌کند.

ما قدرت کامپایلر JIT JAX را در ترکیب با سخت‌افزار موازی {ref}`در بالا <jax_speed>` مشاهده کردیم، هنگامی که `cos` را روی یک آرایه بزرگ اعمال کردیم.

اینجا کامپایل JIT را برای توابع پیچیده‌تر بررسی می‌کنیم

### با NumPy

ابتدا با NumPy امتحان خواهیم کرد، با استفاده از

```{code-cell}
def f(x):
    y = np.cos(2 * x**2) + np.sqrt(np.abs(x)) + 2 * np.sin(x**4) - x**2
    return y
```

بیایید با `x` بزرگ اجرا کنیم

```{code-cell}
n = 50_000_000
x = np.linspace(0, 10, n)
```

```{code-cell}
with qe.Timer():
    # Time NumPy code
    y = f(x)
```

مدل اجرای **Eager**

* هر عملیات بلافاصله پس از مواجهه اجرا می‌شود و نتیجه آن قبل از شروع عملیات بعدی مادی می‌شود.

معایب

* موازی‌سازی حداقلی
* ردپای حافظه سنگین --- آرایه‌های میانی زیادی تولید می‌کند
* خواندن/نوشتن حافظه زیاد

### با JAX

به عنوان اولین مرحله، `np` را در همه جا با `jnp` جایگزین می‌کنیم:

```{code-cell}
def f(x):
    y = jnp.cos(2 * x**2) + jnp.sqrt(jnp.abs(x)) + 2 * jnp.sin(x**4) - x**2
    return y


x = jnp.linspace(0, 10, n)
```

اکنون بیایید آن را زمان‌بندی کنیم.

```{code-cell}
with qe.Timer():
    # First call
    y = f(x)
    # Hold interpreter
    jax.block_until_ready(y);
```

```{code-cell}
with qe.Timer():
    # Second call
    y = f(x)
    # Hold interpreter
    jax.block_until_ready(y);
```

نتیجه مشابه مثال `cos` است --- JAX سریع‌تر است، به ویژه در اجرای دوم پس از کامپایل JIT.

این به این دلیل است که عملیات‌های آرایه‌ای منفرد روی GPU موازی‌سازی می‌شوند

اما ما هنوز از اجرای eager استفاده می‌کنیم

* حافظه زیاد به دلیل آرایه‌های میانی
* خواندن/نوشتن حافظه زیاد

همچنین، هسته‌های جداگانه زیادی روی GPU راه‌اندازی می‌شوند

### کامپایل کل تابع

خوشبختانه، با JAX، ترفند دیگری در آستین داریم --- می‌توانیم کل تابع را JIT-کامپایل کنیم، نه فقط عملیات‌های منفرد.

کامپایلر تمام عملیات‌های آرایه‌ای را در یک هسته بهینه‌شده واحد ادغام می‌کند

بیایید این را با تابع `f` امتحان کنیم:

```{code-cell}
f_jax = jax.jit(f)
```

```{code-cell}
with qe.Timer():
    # First run
    y = f_jax(x)
    # Hold interpreter
    jax.block_until_ready(y);
```

```{code-cell}
with qe.Timer():
    # Second run
    y = f_jax(x)
    # Hold interpreter
    jax.block_until_ready(y);
```

زمان اجرا دوباره بهبود یافته است --- اکنون به این دلیل که تمام عملیات را ادغام کردیم

* بهینه‌سازی تهاجمی بر اساس کل دنباله محاسباتی
* حذف چندین فراخوانی به شتاب‌دهنده سخت‌افزاری

ردپای حافظه نیز بسیار کمتر است --- بدون ایجاد آرایه‌های میانی

اتفاقاً، نحو رایج‌تر هنگام هدف قرار دادن یک تابع برای کامپایلر JIT این است

```{code-cell} ipython3
@jax.jit
def f(x):
    pass # put function body here
```

### نحوه کار کامپایل JIT

هنگامی که `jax.jit` را به یک تابع اعمال می‌کنیم، JAX آن را *ردیابی* می‌کند: به جای اجرای فوری عملیات‌ها، دنباله عملیات‌ها را به صورت یک گراف محاسباتی ثبت می‌کند و آن گراف را به کامپایلر [XLA](https://openxla.org/xla) تحویل می‌دهد.

سپس XLA عملیات‌ها را در یک هسته کامپایل‌شده واحد بهینه‌سازی و ادغام می‌کند که متناسب با سخت‌افزار موجود (CPU، GPU، یا TPU) طراحی شده است.

اولین فراخوانی به یک تابع JIT-کامپایل‌شده سربار کامپایل دارد، اما فراخوانی‌های بعدی با همان شکل‌ها و نوع‌های ورودی از کد کامپایل‌شده کش‌شده استفاده می‌کنند و با سرعت کامل اجرا می‌شوند.

### کامپایل توابع غیرخالص

در حالی که JAX معمولاً هنگام کامپایل توابع ناخالص خطا نمی‌دهد، اجرا غیرقابل پیش‌بینی می‌شود!

در اینجا تصویری از این واقعیت آورده شده است:

```{code-cell} ipython3
a = 1  # global

@jax.jit
def f(x):
    return a + x
```

```{code-cell} ipython3
x = jnp.ones(2)
```

```{code-cell} ipython3
f(x)
```

در کد بالا، مقدار سراسری `a=1` در تابع jitted ادغام می‌شود.

حتی اگر `a` را تغییر دهیم، خروجی `f` تحت تأثیر قرار نخواهد گرفت --- تا زمانی که همان نسخه کامپایل‌شده فراخوانی شود.

```{code-cell} ipython3
a = 42
```

```{code-cell} ipython3
f(x)
```

تغییر بعد ورودی باعث کامپایل مجدد تابع می‌شود، در آن زمان تغییر در مقدار `a` اثر می‌گذارد:

```{code-cell} ipython3
x = jnp.ones(3)
```

```{code-cell} ipython3
f(x)
```

درس اخلاقی داستان: هنگام استفاده از JAX، توابع خالص بنویسید!

## برداری‌سازی با `vmap`

یکی دیگر از تبدیل‌های قدرتمند JAX، `jax.vmap` است که به‌طور خودکار
تابعی که برای یک ورودی منفرد نوشته شده را برداری‌سازی می‌کند تا روی دسته‌ها عمل کند.

این کار نیاز به نوشتن دستی کد برداری‌شده یا استفاده از حلقه‌های صریح را از بین می‌برد.

### یک مثال ساده

فرض کنید تابعی داریم که تفاوت بین میانگین و میانه را برای یک آرایه از اعداد محاسبه می‌کند.

```{code-cell} ipython3
def mm_diff(x):
    return jnp.mean(x) - jnp.median(x)
```

می‌توانیم آن را روی یک بردار منفرد اعمال کنیم:

```{code-cell} ipython3
x = jnp.array([1.0, 2.0, 5.0])
mm_diff(x)
```

حال فرض کنید یک ماتریس داریم و می‌خواهیم این آمارها را برای هر سطر محاسبه کنیم.

بدون `vmap`، به یک حلقه صریح نیاز داریم:

```{code-cell} ipython3
X = jnp.array([[1.0, 2.0, 5.0],
               [4.0, 5.0, 6.0],
               [1.0, 8.0, 9.0]])

for row in X:
    print(mm_diff(row))
```

با این حال، حلقه‌های Python کُند هستند و نمی‌توانند به‌طور کارآمد توسط JAX کامپایل یا موازی‌سازی شوند.

استفاده از `vmap` محاسبه را روی شتاب‌دهنده نگه می‌دارد و با سایر
تبدیل‌های JAX مانند `jit` و `grad` ترکیب می‌شود:

```{code-cell} ipython3
batch_mm_diff = jax.vmap(mm_diff)
batch_mm_diff(X)
```

تابع `mm_diff` برای یک آرایه منفرد نوشته شده بود، و `vmap` به‌طور خودکار
آن را برای عمل سطربه‌سطر روی یک ماتریس ارتقا داد --- بدون حلقه، بدون تغییر شکل.

### ترکیب تبدیل‌ها

یکی از نقاط قوت JAX این است که تبدیل‌ها به‌طور طبیعی با هم ترکیب می‌شوند.

برای مثال، می‌توانیم یک تابع برداری‌شده را با JIT کامپایل کنیم:

```{code-cell} ipython3
fast_batch_mm_diff = jax.jit(jax.vmap(mm_diff))
fast_batch_mm_diff(X)
```

این ترکیب `jit`، `vmap`، و (همان‌طور که در ادامه خواهیم دید) `grad` در قلب
طراحی JAX قرار دارد و آن را به‌ویژه برای محاسبات علمی و یادگیری ماشین بسیار قدرتمند می‌سازد.

## مشتق‌گیری خودکار: یک پیش‌نمایش

JAX می‌تواند از مشتق‌گیری خودکار برای محاسبه گرادیان‌ها استفاده کند.

این ویژگی می‌تواند برای بهینه‌سازی و حل سیستم‌های غیرخطی بسیار مفید باشد.

در اینجا یک مثال ساده با تابع $f(x) = x^2 / 2$ آورده شده است:

```{code-cell} ipython3
def f(x):
    return (x**2) / 2

f_prime = jax.grad(f)
```

```{code-cell} ipython3
f_prime(10.0)
```

بیایید تابع و مشتق آن را رسم کنیم، با توجه به اینکه $f'(x) = x$.

```{code-cell} ipython3
fig, ax = plt.subplots()
x_grid = jnp.linspace(-4, 4, 200)
ax.plot(x_grid, f(x_grid), label="$f$")
ax.plot(x_grid, [f_prime(x) for x in x_grid], label="$f'$")
ax.legend(loc='upper center')
plt.show()
```

مشتق‌گیری خودکار موضوعی عمیق با کاربردهای فراوان در اقتصاد و مالی است. ما یک بررسی جامع‌تر را در {doc}`درس مربوط به مشتق‌گیری خودکار <autodiff>` ارائه می‌دهیم.

## تمرین‌ها


```{exercise-start}
:label: jax_intro_ex2
```

در بخش تمرین {doc}`سخنرانی ما در مورد Numba <numba>`، ما {ref}`از مونت کارلو برای قیمت‌گذاری یک اختیار خرید اروپایی استفاده کردیم <numba_ex4>`.

کد با چندرشته‌ای مبتنی بر Numba تسریع شد.

سعی کنید نسخه‌ای از این عملیات را برای JAX بنویسید، با استفاده از همان پارامترها.



```{exercise-end}
```


```{solution-start} jax_intro_ex2
:class: dropdown
```
در اینجا یک راه‌حل آورده شده است:

```{code-cell} ipython3
M = 10_000_000

n, β, K = 20, 0.99, 100
μ, ρ, ν, S0, h0 = 0.0001, 0.1, 0.001, 10, 0

@jax.jit
def compute_call_price_jax(β=β,
                           μ=μ,
                           S0=S0,
                           h0=h0,
                           K=K,
                           n=n,
                           ρ=ρ,
                           ν=ν,
                           M=M,
                           key=jax.random.key(1)):

    s = jnp.full(M, np.log(S0))
    h = jnp.full(M, h0)

    def update(i, loop_state):
        s, h, key = loop_state
        key, subkey = jax.random.split(key)
        Z = jax.random.normal(subkey, (2, M))
        s = s + μ + jnp.exp(h) * Z[0, :]
        h = ρ * h + ν * Z[1, :]
        new_loop_state = s, h, key
        return new_loop_state

    initial_loop_state = s, h, key
    final_loop_state = jax.lax.fori_loop(0, n, update, initial_loop_state)
    s, h, key = final_loop_state

    expectation = jnp.mean(jnp.maximum(jnp.exp(s) - K, 0))

    return β**n * expectation
```

```{note}
ما از `jax.lax.fori_loop` به جای حلقه `for` پایتون استفاده می‌کنیم.
این به JAX اجازه می‌دهد حلقه را به طور کارآمد بدون باز کردن آن کامپایل کند،
که زمان کامپایل را برای آرایه‌های بزرگ به طور قابل توجهی کاهش می‌دهد.
```

بیایید یک بار آن را اجرا کنیم تا کامپایل شود:

```{code-cell} ipython3
with qe.Timer():
    compute_call_price_jax().block_until_ready()
```

و اکنون بیایید آن را زمان‌بندی کنیم:

```{code-cell} ipython3
with qe.Timer():
    compute_call_price_jax().block_until_ready()
```

```{solution-end}
```
