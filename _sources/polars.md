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
  title: Polars
  headings:
    Overview: مروری کلی
    Series: Series
    DataFrames: DataFrameها
    DataFrames::Selecting data: انتخاب داده
    DataFrames::Filtering by conditions: فیلتر کردن بر اساس شرایط
    DataFrames::Column expressions: عبارات ستونی
    DataFrames::Missing values: مقادیر گم‌شده
    DataFrames::Visualization: تجسم‌سازی
    Lazy evaluation: ارزیابی تنبل
    Lazy evaluation::Eager vs lazy: مشتاق در برابر تنبل
    Lazy evaluation::Query optimization: بهینه‌سازی پرس‌وجو
    Lazy evaluation::Performance comparison: مقایسه عملکرد
    On-line data sources: منابع داده آنلاین
    Exercises: تمرین‌ها
---

(pl)=
```{raw} jupyter
<div id="qe-notebook-header" align="right" style="text-align:right;">
        <a href="https://quantecon.org/" title="quantecon.org">
                <img style="width:250px;display:inline;" width="250px" src="https://assets.quantecon.org/img/qe-menubar-logo.svg" alt="QuantEcon">
        </a>
</div>
```

# Polars

```{index} single: Python; Polars
```

علاوه بر آنچه در Anaconda موجود است، این درس به کتابخانه‌های زیر نیاز دارد:

```{code-cell} ipython3
:tags: [hide-output]

!pip install --upgrade polars yfinance
```

## مروری کلی

[Polars](https://pola.rs/) یک کتابخانه سریع دستکاری داده برای Python است که به زبان Rust نوشته شده است.

این کتابخانه به دلیل مزیت‌های عملکردی خود به عنوان جایگزینی مدرن برای {doc}`pandas <pandas>` محبوبیت قابل‌توجهی کسب کرده است.

Polars با در نظر گرفتن عملکرد و کارایی حافظه طراحی شده و از موارد زیر بهره می‌برد:

* [قالب ستونی Apache Arrow](https://arrow.apache.org/docs/format/Columnar.html) برای دسترسی سریع به داده
* [ارزیابی تنبل](https://en.wikipedia.org/wiki/Lazy_evaluation) برای بهینه‌سازی اجرای پرس‌وجو
* پردازش موازی برای استفاده از تمام هسته‌های پردازنده در دسترس
* یک رابط برنامه‌نویسی گویا که حول عبارات ستونی ساخته شده است

```{tip}
*چرا Polars را به‌جای pandas در نظر بگیریم؟*

* **حافظه**: pandas معمولاً به ۵ تا ۱۰ برابر حجم مجموعه داده شما نیاز به RAM دارد؛ Polars تنها به ۲ تا ۴ برابر نیاز دارد
* **سرعت**: Polars برای بسیاری از عملیات رایج ۱۰ تا ۱۰۰ برابر سریع‌تر است
* **مشاهده کنید**: [معیارهای TPC-H در Polars](https://www.pola.rs/benchmarks/) برای مقایسه‌های عملکردی به‌روز
```

در طول این درس، فرض می‌کنیم که واردسازی‌های زیر انجام شده است

```{code-cell} ipython3
import polars as pl
import numpy as np
import matplotlib.pyplot as plt
```

مانند {doc}`pandas`، Polars دو نوع داده مهم تعریف می‌کند: `Series` و `DataFrame`.

می‌توانید `Series` را به عنوان یک ستون داده در نظر بگیرید، مانند مجموعه‌ای از مشاهدات یک متغیر واحد.

`DataFrame` یک شیء دوبعدی برای ذخیره ستون‌های مرتبط داده است.

## Series

```{index} single: Polars; Series
```

بیایید با Series شروع کنیم.

ابتدا سری‌ای از چهار مشاهده تصادفی می‌سازیم

```{code-cell} ipython3
s = pl.Series(name='daily returns', values=np.random.randn(4))
s
```

```{note}
برخلاف Series در {doc}`pandas <pandas>`، Series در Polars هیچ نمایه ردیفی ندارند.
Polars ستون‌محور است --- دسترسی به داده از طریق عبارات ستونی
و ماسک‌های بولی مدیریت می‌شود، نه برچسب‌های ردیف.
برای جزئیات بیشتر [راهنمای مهاجرت Polars برای کاربران pandas](https://docs.pola.rs/user-guide/migration/pandas/) را ببینید.
```

`Series` در Polars بر پایه آرایه‌های [Apache Arrow](https://arrow.apache.org/) ساخته شده‌اند و از بسیاری از عملیات آشنا پشتیبانی می‌کنند

```{code-cell} ipython3
s * 100
```

مقادیر مطلق به عنوان یک متد در دسترس هستند

```{code-cell} ipython3
s.abs()
```

همچنین می‌توانیم آمار خلاصه سریع دریافت کنیم

```{code-cell} ipython3
s.describe()
```

از آنجا که Polars هیچ نمایه ردیفی ندارد، داده‌های برچسب‌دار به `DataFrame` نیاز دارند.

برای مثال، برای مرتبط کردن نمادهای معاملاتی با بازده‌ها:

```{code-cell} ipython3
df = pl.DataFrame({
    'company': ['AMZN', 'AAPL', 'MSFT', 'GOOG'],
    'daily returns': np.random.randn(4)
})
df
```

با فیلتر کردن بر روی یک عبارت ستونی به یک مقدار دسترسی پیدا می‌کنیم

```{code-cell} ipython3
df.filter(
    pl.col('company') == 'AMZN'
).select('daily returns').item()
```

به‌روزرسانی‌ها نیز از عبارات به‌جای تخصیص نمایه استفاده می‌کنند

```{code-cell} ipython3
df = df.with_columns(
    pl.when(pl.col('company') == 'AMZN')
    .then(0)
    .otherwise(pl.col('daily returns'))
    .alias('daily returns')
)
df
```

می‌توانیم عضویت را نیز بررسی کنیم

```{code-cell} ipython3
'AAPL' in df['company']
```

## DataFrameها

```{index} single: Polars; DataFrames
```

در حالی که `Series` یک ستون منفرد از داده است، `DataFrame` چندین ستون است، یکی برای هر متغیر.

مانند {doc}`pandas`، بیایید با داده‌های [Penn World Tables](https://www.rug.nl/ggdc/productivity/pwt/pwt-releases/pwt-7.0) کار کنیم.

این را با `pl.read_csv` می‌خوانیم

```{code-cell} ipython3
url = ('https://raw.githubusercontent.com/QuantEcon/'
       'lecture-python-programming/main/lectures/_static/'
       'lecture_specific/pandas/data/test_pwt.csv')
df = pl.read_csv(url)
df
```

### انتخاب داده

می‌توانیم ردیف‌ها را با برش‌دهی و ستون‌ها را با نام انتخاب کنیم

```{code-cell} ipython3
df[2:5]
```

برای انتخاب ستون‌های خاص، فهرستی از نام‌ها را به `select` بدهید

```{code-cell} ipython3
df.select(['country', 'tcgdp'])
```

اینها می‌توانند ترکیب شوند

```{code-cell} ipython3
df[2:5].select(['country', 'tcgdp'])
```

### فیلتر کردن بر اساس شرایط

متد `filter` عبارات بولی ساخته‌شده از `pl.col` را می‌پذیرد

```{code-cell} ipython3
df.filter(pl.col('POP') >= 20000)
```

چندین شرط می‌توانند با `&` (و) و `|` (یا) ترکیب شوند

```{code-cell} ipython3
df.filter(
    (pl.col('country').is_in(['Argentina', 'India', 'South Africa'])) &
    (pl.col('POP') > 40000)
)
```

عبارات می‌توانند شامل عملیات حسابی بین ستون‌ها باشند

```{code-cell} ipython3
df.filter(
    (pl.col('cc') + pl.col('cg') >= 80) & (pl.col('POP') <= 20000)
)
```

کشوری با بزرگ‌ترین سهم مصرف خانوار را انتخاب کنید

```{code-cell} ipython3
df.filter(pl.col('cc') == pl.col('cc').max())
```

### عبارات ستونی

یک تفاوت کلیدی با pandas این است که Polars از **عبارات ستونی** برای تبدیل‌ها استفاده می‌کند، نه فراخوانی‌های عنصر به عنصر `apply`.

در اینجا مثالی برای محاسبه بیشینه هر ستون عددی آورده شده است

```{code-cell} ipython3
df.select(
    pl.col(['year', 'POP', 'XRAT', 'tcgdp', 'cc', 'cg'])
    .max()
    .name.suffix('_max')
)
```

عبارات می‌توانند در داخل `with_columns` برای افزودن یا تغییر ستون‌ها استفاده شوند

```{code-cell} ipython3
df.with_columns(
    (pl.col('XRAT') / 10).alias('XRAT_scaled'),
    pl.col(pl.Float64).round(2)
)
```

منطق شرطی از `pl.when(...).then(...).otherwise(...)` استفاده می‌کند

```{code-cell} ipython3
df.with_columns(
    pl.when(pl.col('POP') >= 20000)
    .then(pl.col('POP'))
    .otherwise(None)
    .alias('POP_filtered')
).select(['country', 'POP', 'POP_filtered'])
```

```{note}
Polars `map_elements` را به عنوان راه فراری برای اعمال توابع دلخواه
Python به‌صورت ردیف‌به‌ردیف ارائه می‌دهد، اما این کار موتور بهینه‌شده عبارات
را دور می‌زند و در صورت وجود عبارت بومی باید از آن اجتناب شود.
```

### مقادیر گم‌شده

بیایید برخی مقادیر تهی را برای نمایش تکنیک‌های جایگذاری وارد کنیم

```{code-cell} ipython3
df_nulls = df.with_row_index().with_columns(
    pl.when(pl.col('index') == 0)
    .then(None).otherwise(pl.col('XRAT')).alias('XRAT'),
    pl.when(pl.col('index') == 3)
    .then(None).otherwise(pl.col('cc')).alias('cc'),
    pl.when(pl.col('index') == 5)
    .then(None).otherwise(pl.col('tcgdp')).alias('tcgdp'),
    pl.when(pl.col('index') == 6)
    .then(None).otherwise(pl.col('POP')).alias('POP'),
).drop('index')
df_nulls
```

تمام مقادیر تهی را با صفر پر کنید

```{code-cell} ipython3
df_nulls.fill_null(0)
```

یا با میانگین‌های ستونی پر کنید

```{code-cell} ipython3
cols = ['cc', 'tcgdp', 'POP', 'XRAT']
df_nulls.with_columns(
    pl.col(cols).fill_null(pl.col(cols).mean())
)
```

Polars همچنین از پر کردن رو به جلو (`fill_null(strategy='forward')`) و درون‌یابی پشتیبانی می‌کند.

[ابزارهای جایگذاری پیشرفته‌تری](https://scikit-learn.org/stable/modules/impute.html) در scikit-learn در دسترس هستند.

### تجسم‌سازی

بیایید یک ستون تولید ناخالص داخلی سرانه بسازیم و آن را رسم کنیم

```{code-cell} ipython3
df = (df
    .select(['country', 'POP', 'tcgdp'])
    .rename({'POP': 'population', 'tcgdp': 'total GDP'})
    .with_columns(
        (pl.col('population') * 1e3).alias('population')
    )
    .with_columns(
        (pl.col('total GDP') * 1e6 / pl.col('population'))
        .alias('GDP percap')
    )
    .sort('GDP percap', descending=True)
)
df
```

می‌توانیم ستون‌ها را مستقیماً برای matplotlib استخراج کنیم

```{note}
Polars همچنین یک [رابط برنامه‌نویسی رسم داخلی](https://docs.pola.rs/user-guide/misc/visualization/)
مبتنی بر Altair ارائه می‌دهد (به عنوان مثال، `df.plot.bar(x=..., y=...)`).
ما در اینجا از matplotlib برای هماهنگی با بقیه سری درس‌ها استفاده می‌کنیم.
```

```{code-cell} ipython3
fig, ax = plt.subplots()
ax.bar(df['country'].to_list(), df['GDP percap'].to_list())
ax.set_xlabel('country', fontsize=12)
ax.set_ylabel('GDP per capita', fontsize=12)
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.show()
```

## ارزیابی تنبل

```{index} single: Polars; Lazy Evaluation
```

یکی از قدرتمندترین ویژگی‌های Polars **ارزیابی تنبل** است.

به‌جای اجرای فوری هر عملیات، حالت تنبل کل برنامه پرس‌وجو را جمع‌آوری کرده و پیش از اجرا آن را بهینه می‌کند.

### مشتاق در برابر تنبل

```{code-cell} ipython3
# Reload the dataset
url = ('https://raw.githubusercontent.com/QuantEcon/'
       'lecture-python-programming/main/lectures/_static/'
       'lecture_specific/pandas/data/test_pwt.csv')
df_full = pl.read_csv(url)
```

رابط برنامه‌نویسی **مشتاق (eager)** بلافاصله اجرا می‌شود (مانند pandas)

```{code-cell} ipython3
result_eager = (df_full
    .filter(pl.col('tcgdp') > 1000)
    .select(['country', 'year', 'tcgdp'])
    .sort('tcgdp', descending=True)
)
result_eager.head()
```

رابط برنامه‌نویسی **تنبل (lazy)** در عوض یک برنامه پرس‌وجو می‌سازد

```{code-cell} ipython3
lazy_query = (df_full.lazy()
    .filter(pl.col('tcgdp') > 1000)
    .select(['country', 'year', 'tcgdp'])
    .sort('tcgdp', descending=True)
)
print(lazy_query.explain())
```

برای اجرای برنامه، `collect` را فراخوانی کنید

```{code-cell} ipython3
result_lazy = lazy_query.collect()
result_lazy.head()
```

### بهینه‌سازی پرس‌وجو

موتور تنبل به‌طور خودکار چندین بهینه‌سازی را اعمال می‌کند:

* **فشار محمول (predicate pushdown)** --- فیلترها در اسرع وقت اعمال می‌شوند
* **فشار تصویر (projection pushdown)** --- فقط ستون‌های مورد نیاز از منبع خوانده می‌شوند
* **حذف زیرعبارت مشترک** --- محاسبات تکراری ادغام می‌شوند

بیایید ببینیم Polars چگونه یک پرس‌وجوی چندمرحله‌ای را بازنویسی می‌کند

```{code-cell} ipython3
optimized = (df_full.lazy()
    .select(['country', 'year', 'tcgdp', 'POP'])
    .filter(pl.col('tcgdp') > 500)
    .with_columns(
        (pl.col('tcgdp') / pl.col('POP')).alias('gdp_per_capita')
    )
    .filter(pl.col('gdp_per_capita') > 10)
    .select(['country', 'year', 'gdp_per_capita'])
)

print("Optimized plan:")
print(optimized.explain())
```

اجرای برنامه نتیجه نهایی را به ما می‌دهد

```{code-cell} ipython3
optimized.collect()
```

### مقایسه عملکرد

بیایید pandas، Polars مشتاق، و Polars تنبل را روی همان کار مقایسه کنیم.

ابتدا با یک مجموعه داده کوچک (همان Penn World Tables که در بالا استفاده کردیم) شروع می‌کنیم تا نشان دهیم
که برای داده‌های کوچک تفاوت‌ها ناچیز هستند

```{code-cell} ipython3
import pandas as pd
import time

# Small dataset -- Penn World Tables (~8 rows)
url = ('https://raw.githubusercontent.com/QuantEcon/'
       'lecture-python-programming/main/lectures/_static/'
       'lecture_specific/pandas/data/test_pwt.csv')
small_pd = pd.read_csv(url)
small_pl = pl.read_csv(url)
```

اکنون همان عملیات فیلتر-انتخاب-مرتب‌سازی را در هر کتابخانه زمان‌بندی می‌کنیم

```{code-cell} ipython3
# pandas
start = time.perf_counter()
_ = (small_pd
     .query('tcgdp > 500')
     [['country', 'year', 'tcgdp', 'POP']]
     .assign(gdp_pc=lambda d: d['tcgdp'] / d['POP'])
     .sort_values('gdp_pc', ascending=False))
pd_small = time.perf_counter() - start

# Polars eager
start = time.perf_counter()
_ = (small_pl
     .filter(pl.col('tcgdp') > 500)
     .select(['country', 'year', 'tcgdp', 'POP'])
     .with_columns((pl.col('tcgdp') / pl.col('POP')).alias('gdp_pc'))
     .sort('gdp_pc', descending=True))
pl_small = time.perf_counter() - start

print(f"Small data  --  pandas: {pd_small:.4f}s | Polars eager: {pl_small:.4f}s")
```

روی تعداد اندکی ردیف، تفاوت سرعت ناچیز است --- از هر رابط برنامه‌نویسی که
راحت‌تر می‌یابید استفاده کنید.

اکنون بیایید مقیاس را به ۵ میلیون ردیف بزرگ کنیم که تفاوت واضح می‌شود.

کار این است: فیلتر کردن ردیف‌هایی که `value > 0`، محاسبه یک حاصل‌ضرب وزنی
`value * weight`، سپس گرفتن میانگین آن حاصل‌ضرب در هر گروه ---
یک میانگین وزنی گروه‌بندی‌شده.

```{code-cell} ipython3
n = 5_000_000
np.random.seed(42)

groups = np.random.choice(['A', 'B', 'C', 'D'], n)
values = np.random.randn(n)
weights = np.random.rand(n)
extra1 = np.random.randn(n)
extra2 = np.random.randn(n)

big_pd = pd.DataFrame({
    'group': groups, 'value': values,
    'weight': weights, 'extra1': extra1, 'extra2': extra2
})
big_pl = pl.DataFrame({
    'group': groups, 'value': values,
    'weight': weights, 'extra1': extra1, 'extra2': extra2
})
```

ابتدا، خط پایه pandas

```{code-cell} ipython3
start = time.perf_counter()
tmp = big_pd[big_pd['value'] > 0][['group', 'value', 'weight']].copy()
tmp['weighted'] = tmp['value'] * tmp['weight']
_ = tmp.groupby('group')['weighted'].mean()
pd_time = time.perf_counter() - start
print(f"pandas:       {pd_time:.4f}s")
```

سپس، Polars در حالت مشتاق

```{code-cell} ipython3
start = time.perf_counter()
_ = (big_pl
    .filter(pl.col('value') > 0)
    .select(['group', 'value', 'weight'])
    .with_columns(
        (pl.col('value') * pl.col('weight')).alias('weighted'))
    .group_by('group')
    .agg(pl.col('weighted').mean()))
eager_time = time.perf_counter() - start
print(f"Polars eager: {eager_time:.4f}s")
```

و در نهایت، Polars در حالت تنبل

```{code-cell} ipython3
start = time.perf_counter()
_ = (big_pl.lazy()
    .filter(pl.col('value') > 0)
    .select(['group', 'value', 'weight'])
    .with_columns(
        (pl.col('value') * pl.col('weight')).alias('weighted'))
    .group_by('group')
    .agg(pl.col('weighted').mean())
    .collect())
lazy_time = time.perf_counter() - start
print(f"Polars lazy:  {lazy_time:.4f}s")
```

نکته کلیدی:

* برای **داده‌های کوچک** (هزاران ردیف)، pandas و Polars به‌طور
  مشابهی عمل می‌کنند --- بر اساس ترجیح رابط برنامه‌نویسی و تناسب اکوسیستم انتخاب کنید.
* برای **داده‌های متوسط تا بزرگ** (صدها هزار ردیف و بیشتر)،
  Polars می‌تواند به‌طور قابل‌توجهی سریع‌تر باشد به لطف موتور Rust، اجرای موازی،
  و (در حالت تنبل) بهینه‌سازی پرس‌وجو.

رابط برنامه‌نویسی تنبل هنگام خواندن از دیسک به‌ویژه قدرتمند است --- `scan_csv` مستقیماً یک `LazyFrame` برمی‌گرداند، بنابراین فیلترها و تصویرها به خواننده فایل فشار داده می‌شوند.

```{tip}
از `pl.scan_csv(path)` به‌جای `pl.read_csv(path)` هنگام کار با
فایل‌های CSV بزرگ استفاده کنید.
فقط ستون‌ها و ردیف‌هایی که واقعاً به آن‌ها نیاز دارید از دیسک خوانده می‌شوند.
[مستندات ورودی/خروجی Polars](https://docs.pola.rs/user-guide/io/csv/) را ببینید.
```

## منابع داده آنلاین

```{index} single: Data Sources
```

مانند {doc}`pandas`، Python پرس‌وجوی پایگاه‌های داده آنلاین را ساده می‌کند.

یک پایگاه داده مهم برای اقتصاددانان [FRED](https://fred.stlouisfed.org/) است --- مجموعه‌ای گسترده از داده‌های سری زمانی که توسط فدرال رزرو سنت‌لوئیس نگهداری می‌شود.

متد `read_csv` در Polars می‌تواند داده را مستقیماً از یک URL دریافت کند.

از `try_parse_dates=True` برای تجزیه خودکار ستون تاریخ استفاده می‌کنیم

```{code-cell} ipython3
fred_url = ('https://fred.stlouisfed.org/graph/fredgraph.csv?'
            'bgcolor=%23e1e9f0&chart_type=line&drp=0&'
            'fo=open%20sans&graph_bgcolor=%23ffffff&'
            'height=450&mode=fred&recession_bars=on&'
            'txtcolor=%23444444&ts=12&tts=12&width=1318&'
            'nt=0&thu=0&trc=0&show_legend=yes&'
            'show_axis_titles=yes&show_tooltip=yes&'
            'id=UNRATE&scale=left&cosd=1948-01-01&'
            'coed=2024-06-01&line_color=%234572a7&'
            'link_values=false&line_style=solid&'
            'mark_type=none&mw=3&lw=2&ost=-99999&'
            'oet=99999&mma=0&fml=a&fq=Monthly&fam=avg&'
            'fgst=lin&fgsnd=2020-02-01&line_index=1&'
            'transformation=lin&vintage_date=2024-07-29&'
            'revision_date=2024-07-29&nd=1948-01-01')
data = pl.read_csv(fred_url, try_parse_dates=True)
```

بیایید چند ردیف اول را بررسی کنیم

```{code-cell} ipython3
data.head()
```

و آمار خلاصه را دریافت کنیم

```{code-cell} ipython3
data.describe()
```

نرخ بیکاری از ۲۰۰۶ تا ۲۰۱۲ را رسم کنید

```{code-cell} ipython3
filtered = data.filter(
    (pl.col('observation_date') >= pl.date(2006, 1, 1)) &
    (pl.col('observation_date') <= pl.date(2012, 12, 31))
)

fig, ax = plt.subplots()
ax.plot(filtered['observation_date'].to_list(),
        filtered['UNRATE'].to_list())
ax.set_title('US Unemployment Rate')
ax.set_xlabel('year', fontsize=12)
ax.set_ylabel('%', fontsize=12)
plt.show()
```

Polars از [فرمت‌های فایل بسیاری](https://docs.pola.rs/user-guide/io/) از جمله Excel، JSON، Parquet، و اتصالات مستقیم پایگاه داده پشتیبانی می‌کند.

## تمرین‌ها

```{exercise-start}
:label: pl_ex1
```

با این واردسازی‌ها:

```{code-cell} ipython3
import datetime as dt
import yfinance as yf
```

برنامه‌ای بنویسید که تغییر درصدی قیمت را در طول سال ۲۰۲۱ برای سهام‌های زیر محاسبه کند:

```{code-cell} ipython3
ticker_list = {'INTC': 'Intel',
               'MSFT': 'Microsoft',
               'IBM': 'IBM',
               'BHP': 'BHP',
               'TM': 'Toyota',
               'AAPL': 'Apple',
               'AMZN': 'Amazon',
               'C': 'Citigroup',
               'QCOM': 'Qualcomm',
               'KO': 'Coca-Cola',
               'GOOG': 'Google'}
```

در اینجا تابعی است که قیمت‌های بسته شدن را در یک DataFrame Polars می‌خواند:

```{code-cell} ipython3
def read_data_polars(ticker_list,
                     start=dt.datetime(2021, 1, 1),
                     end=dt.datetime(2021, 12, 31)):
    """
    Read closing price data from Yahoo Finance
    and return a Polars DataFrame.
    """
    dataframes = []

    for tick in ticker_list:
        stock = yf.Ticker(tick)
        prices = stock.history(start=start, end=end)
        df = pl.DataFrame({
            'Date': list(prices.index.date),
            tick: prices['Close'].values
        }).with_columns(pl.col('Date').cast(pl.Date))
        dataframes.append(df)

    result = dataframes[0]
    for df in dataframes[1:]:
        result = result.join(
            df, on='Date', how='full', coalesce=True
        )
    return result.sort('Date')

ticker = read_data_polars(ticker_list)
```

```{note}
اتصال‌های Polars ترتیب ردیف‌های خروجی را تضمین نمی‌کنند --- کلیدهایی که فقط در یک طرف
مطابقت دارند به‌جای قرار گرفتن در جای خود، اضافه می‌شوند.
این همان تم "بدون نمایه، بدون تراز خودکار" از بالاست: بدون برچسب‌های
ردیف برای تراز کردن، ترتیب چیزی است که به‌طور صریح درخواست می‌کنیم.
به همین دلیل `sort('Date')` قبل از بازگشت قرار دارد، که هر محاسبه بعدی
`first()`/`last()` به آن متکی است.
```

برنامه را برای رسم نتیجه به عنوان یک نمودار میله‌ای کامل کنید.

```{exercise-end}
```

```{solution-start} pl_ex1
:class: dropdown
```

تغییرات درصدی را با استفاده از عبارات Polars محاسبه کنید:

```{code-cell} ipython3
price_change = ticker.select([
    ((pl.col(tick).last() / pl.col(tick).first() - 1) * 100)
    .alias(tick)
    for tick in ticker_list.keys()
]).transpose(
    include_header=True,
    header_name='ticker',
    column_names=['pct_change']
).with_columns(
    pl.col('ticker')
    .replace_strict(ticker_list, default=pl.col('ticker'))
    .alias('company')
).sort('pct_change')

print(price_change)
```

نتایج را مستقیماً با matplotlib رسم کنید:

```{code-cell} ipython3
companies = price_change['company'].to_list()
changes = price_change['pct_change'].to_list()
colors = ['red' if x < 0 else 'blue' for x in changes]

fig, ax = plt.subplots(figsize=(10, 8))
ax.bar(companies, changes, color=colors)
ax.set_xlabel('stock', fontsize=12)
ax.set_ylabel('percentage change in price', fontsize=12)
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.show()
```

```{solution-end}
```


```{exercise-start}
:label: pl_ex2
```

با استفاده از `read_data_polars` از {ref}`pl_ex1`، تغییر درصدی سال به سال را برای این شاخص‌ها بدست آورید:

```{code-cell} ipython3
indices_list = {'^GSPC': 'S&P 500',
               '^IXIC': 'NASDAQ',
               '^DJI': 'Dow Jones',
               '^N225': 'Nikkei'}
```

نتیجه را به عنوان یک نمودار سری زمانی رسم کنید.

```{exercise-end}
```

```{solution-start} pl_ex2
:class: dropdown
```

```{code-cell} ipython3
indices_data = read_data_polars(
    indices_list,
    start=dt.datetime(1971, 1, 1),
    end=dt.datetime(2021, 12, 31)
)

indices_data = indices_data.with_columns(
    pl.col('Date').dt.year().alias('year')
)
```

بازده‌های سالانه را با استفاده از عملیات گروه‌بندی محاسبه کنید:

```{code-cell} ipython3
yearly_returns = indices_data.group_by('year').agg([
    *[pl.col(idx).drop_nulls().first().alias(f'{idx}_first')
      for idx in indices_list],
    *[pl.col(idx).drop_nulls().last().alias(f'{idx}_last')
      for idx in indices_list]
])

for idx, name in indices_list.items():
    yearly_returns = yearly_returns.with_columns(
        ((pl.col(f'{idx}_last') - pl.col(f'{idx}_first'))
         / pl.col(f'{idx}_first') * 100).alias(name)
    )

yearly_returns = (yearly_returns
    .select(['year', *indices_list.values()])
    .sort('year')
)
print(yearly_returns)
```

آمار خلاصه:

```{code-cell} ipython3
yearly_returns.select(list(indices_list.values())).describe()
```

هر شاخص را در یک زیرنمودار رسم کنید:

```{code-cell} ipython3
fig, axes = plt.subplots(2, 2, figsize=(12, 10))
years = yearly_returns['year'].to_list()

for iter_, ax in enumerate(axes.flatten()):
    name = list(indices_list.values())[iter_]
    values = yearly_returns[name].to_list()
    ax.plot(years, values, 'o-', linewidth=2, markersize=4)
    ax.axhline(y=0, color='k', linestyle='--', alpha=0.3)
    ax.set_ylabel('yearly return (%)', fontsize=12)
    ax.set_xlabel('year', fontsize=12)
    ax.set_title(name, fontsize=12)

plt.tight_layout()
plt.show()
```

```{solution-end}
```