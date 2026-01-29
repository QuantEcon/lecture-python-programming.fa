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
heading-map:
  overview: مرور کلی
  series: Series
  dataframes: DataFrames
  select-data-by-position: انتخاب داده بر اساس موقعیت
  select-data-by-conditions: انتخاب داده بر اساس شرایط
  apply-method: متد Apply
  make-changes-in-dataframes: ایجاد تغییرات در DataFrames
  standardization-and-visualization: استانداردسازی و بصری‌سازی
  on-line-data-sources: منابع داده آنلاین
  accessing-data-with-indexrequests-single-requests: 'دسترسی به داده با {index}`requests <single: requests>`'
  using-indexwbgapi-single-wbgapi-and-indexyfinance-single-yfinance-to-access-data: 'استفاده از {index}`wbgapi <single: wbgapi>` و {index}`yfinance <single: yfinance>` برای دسترسی به داده'
  exercises: تمرین‌ها
---

(pd)=
```{raw} jupyter
<div id="qe-notebook-header" align="right" style="text-align:right;">
        <a href="https://quantecon.org/" title="quantecon.org">
                <img style="width:250px;display:inline;" width="250px" src="https://assets.quantecon.org/img/qe-menubar-logo.svg" alt="QuantEcon">
        </a>
</div>
```

# {index}`Pandas <single: Pandas>`

```{index} single: Python; Pandas
```

علاوه بر آنچه در Anaconda موجود است، این درس به کتابخانه‌های زیر نیاز دارد:

```{code-cell} ipython3
:tags: [hide-output]

!pip install --upgrade wbgapi
!pip install --upgrade yfinance
```

## مرور کلی

[Pandas](https://pandas.pydata.org/) یک بسته ابزارهای سریع و کارآمد برای تحلیل داده در Python است.

محبوبیت آن در سال‌های اخیر به طور همزمان با ظهور حوزه‌هایی مانند علم داده و یادگیری ماشین افزایش یافته است.

در اینجا مقایسه‌ای از محبوبیت در طول زمان در برابر Matlab و STATA از طریق Stack Overflow Trends آورده شده است

```{figure} /_static/lecture_specific/pandas/pandas_vs_rest.png
:scale: 100
```

همانطور که [NumPy](https://numpy.org/) نوع داده آرایه پایه به اضافه عملیات اصلی آرایه را فراهم می‌کند، pandas

1. ساختارهای بنیادین برای کار با داده را تعریف می‌کند و
1. آنها را با متدهایی مجهز می‌کند که عملیات‌هایی مانند موارد زیر را تسهیل می‌کنند:
    * خواندن داده
    * تنظیم اندیس‌ها
    * کار با تاریخ‌ها و سری‌های زمانی
    * مرتب‌سازی، گروه‌بندی، مرتب‌سازی مجدد و به طور کلی تبدیل داده [^mung]
    * برخورد با مقادیر گمشده، و غیره

قابلیت‌های آماری پیچیده‌تر به بسته‌های دیگر مانند [statsmodels](https://www.statsmodels.org/) و [scikit-learn](https://scikit-learn.org/) که بر روی pandas ساخته شده‌اند، واگذار می‌شود.

این درس مقدمه‌ای پایه به pandas ارائه خواهد داد.

در سراسر درس، فرض خواهیم کرد که import‌های زیر انجام شده است

```{code-cell} ipython3
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import requests
```

دو نوع داده مهم تعریف شده توسط pandas، `Series` و `DataFrame` هستند.

می‌توانید `Series` را به عنوان یک "ستون" از داده تصور کنید، مانند مجموعه‌ای از مشاهدات روی یک متغیر واحد.

`DataFrame` یک شیء دوبعدی برای ذخیره ستون‌های مرتبط از داده است.

## Series

```{index} single: Pandas; Series
```

بیایید با Series شروع کنیم.

با ایجاد یک سری از چهار مشاهده تصادفی شروع می‌کنیم

```{code-cell} ipython3
s = pd.Series(np.random.randn(4), name='daily returns')
s
```

در اینجا می‌توانید تصور کنید که اندیس‌های `0, 1, 2, 3` چهار شرکت فهرست شده را اندیس‌گذاری می‌کنند و مقادیر، بازده روزانه سهام آنها هستند.

`Series` در Pandas بر روی آرایه‌های NumPy ساخته شده‌اند و از بسیاری از عملیات مشابه پشتیبانی می‌کنند

```{code-cell} ipython3
s * 100
```

```{code-cell} ipython3
np.abs(s)
```

اما `Series` بیش از آرایه‌های NumPy ارائه می‌دهند.

نه تنها آنها چند متد اضافی (با جهت‌گیری آماری) دارند

```{code-cell} ipython3
s.describe()
```

بلکه اندیس‌های آنها انعطاف‌پذیرتر هستند

```{code-cell} ipython3
s.index = ['AMZN', 'AAPL', 'MSFT', 'GOOG']
s
```

از این منظر، `Series` مانند دیکشنری‌های سریع و کارآمد Python هستند (با محدودیت اینکه تمام آیتم‌های دیکشنری از نوع یکسانی دارند --- در این مورد، اعداد اعشاری).

در واقع، می‌توانید از بسیاری از نحو یکسان با دیکشنری‌های Python استفاده کنید

```{code-cell} ipython3
s['AMZN']
```

```{code-cell} ipython3
s['AMZN'] = 0
s
```

```{code-cell} ipython3
'AAPL' in s
```

## DataFrames

```{index} single: Pandas; DataFrames
```

در حالی که `Series` یک ستون واحد از داده است، `DataFrame` چندین ستون است، یکی برای هر متغیر.

در اصل، یک `DataFrame` در pandas مشابه یک صفحه گسترده Excel (بسیار بهینه شده) است.

بنابراین، یک ابزار قدرتمند برای نمایش و تحلیل داده‌هایی است که به طور طبیعی در سطرها و ستون‌ها سازماندهی شده‌اند، اغلب با اندیس‌های توصیفی برای سطرها و ستون‌های فردی.

بیایید به مثالی نگاه کنیم که داده را از فایل CSV `pandas/data/test_pwt.csv` می‌خواند، که از [Penn World Tables](https://www.rug.nl/ggdc/productivity/pwt/pwt-releases/pwt-7.0) گرفته شده است.

مجموعه داده شامل شاخص‌های زیر است

| Variable Name | Description |
| :-: | :-: |
| POP | جمعیت (به هزار نفر) |
| XRAT | نرخ ارز به دلار آمریکا |                     
| tcgdp | کل تولید ناخالص داخلی تبدیل شده PPP (به میلیون دلار بین‌المللی) |
| cc | سهم مصرف از تولید ناخالص داخلی تبدیل شده PPP سرانه (٪) |
| cg | سهم مصرف دولت از تولید ناخالص داخلی تبدیل شده PPP سرانه (٪) |

ما این را از یک URL با استفاده از تابع `read_csv` در `pandas` خواهیم خواند.

```{code-cell} ipython3
df = pd.read_csv('https://raw.githubusercontent.com/QuantEcon/lecture-python-programming/master/source/_static/lecture_specific/pandas/data/test_pwt.csv')
type(df)
```

در اینجا محتوای `test_pwt.csv` آمده است

```{code-cell} ipython3
df
```

### انتخاب داده بر اساس موقعیت

در عمل، کاری که همیشه انجام می‌دهیم این است که زیرمجموعه‌ای از داده مورد علاقه خود را پیدا کنیم، انتخاب کنیم و با آن کار کنیم.

می‌توانیم سطرهای خاص را با استفاده از نماد برش استاندارد آرایه Python انتخاب کنیم

```{code-cell} ipython3
df[2:5]
```

برای انتخاب ستون‌ها، می‌توانیم لیستی حاوی نام ستون‌های مورد نظر را به صورت رشته ارسال کنیم

```{code-cell} ipython3
df[['country', 'tcgdp']]
```

برای انتخاب هم سطرها و هم ستون‌ها با استفاده از اعداد صحیح، باید از ویژگی `iloc` با قالب `.iloc[rows, columns]` استفاده شود.

```{code-cell} ipython3
df.iloc[2:5, 0:4]
```

برای انتخاب سطرها و ستون‌ها با استفاده از ترکیبی از اعداد صحیح و برچسب‌ها، می‌توان از ویژگی `loc` به روشی مشابه استفاده کرد

```{code-cell} ipython3
df.loc[df.index[2:5], ['country', 'tcgdp']]
```

### انتخاب داده بر اساس شرایط

به جای اندیس‌گذاری سطرها و ستون‌ها با استفاده از اعداد صحیح و نام‌ها، می‌توانیم یک زیر دیتافریم از علایق خود را که شرایط خاص (بالقوه پیچیده) را برآورده می‌کند، به دست آوریم.

این بخش روش‌های مختلف انجام این کار را نشان می‌دهد.

ساده‌ترین راه با عملگر `[]` است.

```{code-cell} ipython3
df[df.POP >= 20000]
```

برای درک آنچه در اینجا اتفاق می‌افتد، توجه کنید که `df.POP >= 20000` یک سری از مقادیر بولی را برمی‌گرداند.

```{code-cell} ipython3
df.POP >= 20000
```

در این مورد، `df[___]` یک سری از مقادیر بولی را می‌گیرد و تنها سطرهایی با مقادیر `True` را برمی‌گرداند.

یک مثال دیگر بزنید،

```{code-cell} ipython3
df[(df.country.isin(['Argentina', 'India', 'South Africa'])) & (df.POP > 40000)]
```

با این حال، راه دیگری برای انجام همین کار وجود دارد که می‌تواند برای دیتافریم‌های بزرگ کمی سریع‌تر باشد، با نحو طبیعی‌تر.

```{code-cell} ipython3
# مورد بالا معادل است با 
df.query("POP >= 20000")
```

```{code-cell} ipython3
df.query("country in ['Argentina', 'India', 'South Africa'] and POP > 40000")
```

همچنین می‌توانیم عملیات حسابی بین ستون‌های مختلف را مجاز کنیم.

```{code-cell} ipython3
df[(df.cc + df.cg >= 80) & (df.POP <= 20000)]
```

```{code-cell} ipython3
# مورد بالا معادل است با 
df.query("cc + cg >= 80 & POP <= 20000")
```

به عنوان مثال، می‌توانیم از شرط‌گذاری برای انتخاب کشوری با بیشترین سهم مصرف خانوار - تولید ناخالص داخلی `cc` استفاده کنیم.

```{code-cell} ipython3
df.loc[df.cc == max(df.cc)]
```

وقتی فقط می‌خواهیم به ستون‌های خاصی از یک زیر دیتافریم انتخاب شده نگاه کنیم، می‌توانیم از شرایط بالا با دستور `.loc[__ , __]` استفاده کنیم.

آرگومان اول شرط را می‌گیرد، در حالی که آرگومان دوم لیستی از ستون‌هایی را که می‌خواهیم برگردانیم می‌گیرد.

```{code-cell} ipython3
df.loc[(df.cc + df.cg >= 80) & (df.POP <= 20000), ['country', 'year', 'POP']]
```

**کاربرد: زیرمجموعه‌سازی دیتافریم**

مجموعه داده‌های دنیای واقعی می‌توانند [عظیم](https://developers.google.com/machine-learning/crash-course/overfitting) باشند.

گاهی اوقات مطلوب است که با زیرمجموعه‌ای از داده کار کنیم تا کارایی محاسباتی را افزایش دهیم و افزونگی را کاهش دهیم.

فرض کنید که ما فقط به جمعیت (`POP`) و کل تولید ناخالص داخلی (`tcgdp`) علاقه‌مند هستیم.

یکی از راه‌های کاهش دیتافریم `df` به فقط این متغیرها، بازنویسی دیتافریم با استفاده از روش انتخاب توضیح داده شده در بالا است

```{code-cell} ipython3
df_subset = df[['country', 'POP', 'tcgdp']]
df_subset
```

سپس می‌توانیم مجموعه داده کوچکتر را برای تحلیل بیشتر ذخیره کنیم.

```{code-block} python3
:class: no-execute

df_subset.to_csv('pwt_subset.csv', index=False)
```

### متد Apply

یکی دیگر از متدهای پرکاربرد Pandas، `df.apply()` است.

این یک تابع را به هر سطر/ستون اعمال می‌کند و یک سری را برمی‌گرداند.

این تابع می‌تواند برخی از توابع داخلی مانند تابع `max`، یک تابع `lambda` یا یک تابع تعریف شده توسط کاربر باشد.

در اینجا مثالی با استفاده از تابع `max` آورده شده است

```{code-cell} ipython3
df[['year', 'POP', 'XRAT', 'tcgdp', 'cc', 'cg']].apply(max)
```

این خط کد تابع `max` را به تمام ستون‌های انتخاب شده اعمال می‌کند.

تابع `lambda` اغلب با متد `df.apply()` استفاده می‌شود

یک مثال بدیهی برگرداندن خودش برای هر سطر در دیتافریم است

```{code-cell} ipython3
df.apply(lambda row: row, axis=1)
```

```{note}
برای متد `.apply()`
- axis = 0 -- اعمال تابع به هر ستون (متغیرها)
- axis = 1 -- اعمال تابع به هر سطر (مشاهدات)
- axis = 0 پارامتر پیش‌فرض است
```

می‌توانیم آن را همراه با `.loc[]` برای انجام برخی انتخاب‌های پیشرفته‌تر استفاده کنیم.

```{code-cell} ipython3
complexCondition = df.apply(
    lambda row: row.POP > 40000 if row.country in ['Argentina', 'India', 'South Africa'] else row.POP < 20000, 
    axis=1), ['country', 'year', 'POP', 'XRAT', 'tcgdp']
```

`df.apply()` در اینجا یک سری از مقادیر بولی سطرهایی را که شرط مشخص شده در عبارت if-else را برآورده می‌کنند برمی‌گرداند.

علاوه بر این، زیرمجموعه‌ای از متغیرهای مورد علاقه را نیز تعریف می‌کند.

```{code-cell} ipython3
complexCondition
```

وقتی این شرط را به دیتافریم اعمال کنیم، نتیجه خواهد بود

```{code-cell} ipython3
df.loc[complexCondition]
```

### ایجاد تغییرات در DataFrames

توانایی ایجاد تغییرات در دیتافریم‌ها برای تولید یک مجموعه داده تمیز برای تحلیل آینده مهم است.

**1.** می‌توانیم به راحتی از `df.where()` برای "نگه داشتن" سطرهایی که انتخاب کرده‌ایم استفاده کنیم و بقیه سطرها را با هر مقدار دیگری جایگزین کنیم

```{code-cell} ipython3
df.where(df.POP >= 20000, False)
```

**2.** به سادگی می‌توانیم از `.loc[]` برای مشخص کردن ستونی که می‌خواهیم تغییر دهیم استفاده کنیم و مقادیر را اختصاص دهیم

```{code-cell} ipython3
df.loc[df.cg == max(df.cg), 'cg'] = np.nan
df
```

**3.** می‌توانیم از متد `.apply()` برای تغییر *سطرها/ستون‌ها به عنوان یک کل* استفاده کنیم

```{code-cell} ipython3
def update_row(row):
    # تغییر POP
    row.POP = np.nan if row.POP<= 10000 else row.POP

    # تغییر XRAT
    row.XRAT = row.XRAT / 10
    return row

df.apply(update_row, axis=1)
```

**4.** می‌توانیم از متد `.map()` برای تغییر تمام *ورودی‌های فردی* در دیتافریم با هم استفاده کنیم.

```{code-cell} ipython3
# گرد کردن تمام اعداد اعشاری به 2 رقم اعشار
df.map(lambda x : round(x,2) if type(x)!=str else x)
```

**کاربرد: جایگزینی مقدار گمشده**

جایگزینی مقادیر گمشده یک گام مهم در تبدیل داده است.

بیایید به طور تصادفی چند مقدار NaN وارد کنیم

```{code-cell} ipython3
for idx in list(zip([0, 3, 5, 6], [3, 4, 6, 2])):
    df.iloc[idx] = np.nan

df
```

تابع `zip()` در اینجا جفت‌هایی از مقادیر دو لیست را ایجاد می‌کند (یعنی [0,3]، [3,4] ...)

می‌توانیم دوباره از متد `.map()` برای جایگزینی تمام مقادیر گمشده با 0 استفاده کنیم

```{code-cell} ipython3
# جایگزینی تمام مقادیر NaN با 0
def replace_nan(x):
    if type(x)!=str:
        return  0 if np.isnan(x) else x
    else:
        return x

df.map(replace_nan)
```

Pandas همچنین متدهای مناسبی برای جایگزینی مقادیر گمشده در اختیار ما قرار می‌دهد.

به عنوان مثال، جایگزینی تکی با استفاده از میانگین متغیرها به راحتی در pandas قابل انجام است

```{code-cell} ipython3
df = df.fillna(df.iloc[:,2:8].mean())
df
```

جایگزینی مقدار گمشده یک حوزه بزرگ در علم داده است که شامل تکنیک‌های مختلف یادگیری ماشین می‌شود.

[ابزارهای پیشرفته‌تر](https://scikit-learn.org/stable/modules/impute.html) در python برای جایگزینی مقادیر گمشده نیز وجود دارد.

### استانداردسازی و بصری‌سازی

فرض کنیم که ما فقط به جمعیت (`POP`) و کل تولید ناخالص داخلی (`tcgdp`) علاقه‌مند هستیم.

یکی از راه‌های کاهش دیتافریم `df` به فقط این متغیرها، بازنویسی دیتافریم با استفاده از روش انتخاب توضیح داده شده در بالا است

```{code-cell} ipython3
df = df[['country', 'POP', 'tcgdp']]
df
```

در اینجا اندیس `0, 1,..., 7` زائد است زیرا می‌توانیم از نام کشورها به عنوان اندیس استفاده کنیم.

برای انجام این کار، اندیس را به متغیر `country` در دیتافریم تنظیم می‌کنیم

```{code-cell} ipython3
df = df.set_index('country')
df
```

بیایید نام‌های بهتری به ستون‌ها بدهیم

```{code-cell} ipython3
df.columns = 'population', 'total GDP'
df
```

متغیر `population` به هزار نفر است، بیایید به واحدهای تکی برگردیم

```{code-cell} ipython3
df['population'] = df['population'] * 1e3
df
```

در مرحله بعد، قصد داریم ستونی که تولید ناخالص داخلی واقعی سرانه را نشان می‌دهد اضافه کنیم، و در ضمن در 1,000,000 ضرب می‌کنیم زیرا کل تولید ناخالص داخلی به میلیون است

```{code-cell} ipython3
df['GDP percap'] = df['total GDP'] * 1e6 / df['population']
df
```

یکی از ویژگی‌های خوب اشیاء `DataFrame` و `Series` در pandas این است که آنها متدهایی برای رسم نمودار و بصری‌سازی دارند که از طریق Matplotlib کار می‌کنند.

به عنوان مثال، می‌توانیم به راحتی یک نمودار میله‌ای از تولید ناخالص داخلی سرانه تولید کنیم

```{code-cell} ipython3
ax = df['GDP percap'].plot(kind='bar')
ax.set_xlabel('country', fontsize=12)
ax.set_ylabel('GDP per capita', fontsize=12)
plt.show()
```

در حال حاضر دیتافریم به ترتیب الفبایی بر اساس کشورها مرتب شده است --- بیایید آن را به تولید ناخالص داخلی سرانه تغییر دهیم

```{code-cell} ipython3
df = df.sort_values(by='GDP percap', ascending=False)
df
```

رسم نمودار مانند قبل اکنون به نتیجه زیر منجر می‌شود

```{code-cell} ipython3
ax = df['GDP percap'].plot(kind='bar')
ax.set_xlabel('country', fontsize=12)
ax.set_ylabel('GDP per capita', fontsize=12)
plt.show()
```

## منابع داده آنلاین

```{index} single: Data Sources
```

Python پرس‌وجوی برنامه‌ای پایگاه‌های داده آنلاین را ساده می‌کند.

یک پایگاه داده مهم برای اقتصاددانان [FRED](https://fred.stlouisfed.org/) است --- یک مجموعه وسیع از داده‌های سری زمانی که توسط فدرال رزرو سنت لوئیس نگهداری می‌شود.

به عنوان مثال، فرض کنید که ما به [نرخ بیکاری](https://fred.stlouisfed.org/series/UNRATE) علاقه‌مند هستیم.

(برای دانلود داده به عنوان csv، روی `Download` در بالا سمت راست کلیک کرده و گزینه `CSV (data)` را انتخاب کنید).

از طرف دیگر، می‌توانیم به فایل CSV از داخل یک برنامه Python دسترسی پیدا کنیم.

این کار می‌تواند با انواع روش‌ها انجام شود.

ما با یک روش نسبتاً سطح پایین شروع می‌کنیم و سپس به pandas برمی‌گردیم.

### دسترسی به داده با {index}`requests <single: requests>`

```{index} single: Python; requests
```

یک گزینه استفاده از [requests](https://requests.readthedocs.io/en/latest/) است، یک کتابخانه استاندارد Python برای درخواست داده از طریق اینترنت.

برای شروع، کد زیر را روی کامپیوتر خود امتحان کنید

```{code-cell} ipython3
r = requests.get('https://fred.stlouisfed.org/graph/fredgraph.csv?bgcolor=%23e1e9f0&chart_type=line&drp=0&fo=open%20sans&graph_bgcolor=%23ffffff&height=450&mode=fred&recession_bars=on&txtcolor=%23444444&ts=12&tts=12&width=1318&nt=0&thu=0&trc=0&show_legend=yes&show_axis_titles=yes&show_tooltip=yes&id=UNRATE&scale=left&cosd=1948-01-01&coed=2024-06-01&line_color=%234572a7&link_values=false&line_style=solid&mark_type=none&mw=3&lw=2&ost=-99999&oet=99999&mma=0&fml=a&fq=Monthly&fam=avg&fgst=lin&fgsnd=2020-02-01&line_index=1&transformation=lin&vintage_date=2024-07-29&revision_date=2024-07-29&nd=1948-01-01')
```

اگر پیام خطایی نباشد، پس فراخوانی موفق بوده است.

اگر خطایی دریافت کردید، دو علت احتمالی وجود دارد

1. شما به اینترنت متصل نیستید --- امیدوارم که این مورد نباشد.
1. دستگاه شما از طریق یک سرور پروکسی به اینترنت دسترسی پیدا می‌کند و Python از این موضوع آگاه نیست.

در حالت دوم، می‌توانید

* به دستگاه دیگری تغییر دهید
* مشکل پروکسی خود را با خواندن [مستندات](https://requests.readthedocs.io/en/latest/) حل کنید

با فرض اینکه همه چیز کار می‌کند، اکنون می‌توانید از شیء `source` برگردانده شده توسط فراخوانی `requests.get('https://research.stlouisfed.org/fred2/series/UNRATE/downloaddata/UNRATE.csv')` استفاده کنید

```{code-cell} ipython3
url = 'https://fred.stlouisfed.org/graph/fredgraph.csv?bgcolor=%23e1e9f0&chart_type=line&drp=0&fo=open%20sans&graph_bgcolor=%23ffffff&height=450&mode=fred&recession_bars=on&txtcolor=%23444444&ts=12&tts=12&width=1318&nt=0&thu=0&trc=0&show_legend=yes&show_axis_titles=yes&show_tooltip=yes&id=UNRATE&scale=left&cosd=1948-01-01&coed=2024-06-01&line_color=%234572a7&link_values=false&line_style=solid&mark_type=none&mw=3&lw=2&ost=-99999&oet=99999&mma=0&fml=a&fq=Monthly&fam=avg&fgst=lin&fgsnd=2020-02-01&line_index=1&transformation=lin&vintage_date=2024-07-29&revision_date=2024-07-29&nd=1948-01-01'
source = requests.get(url).content.decode().split("\n")
source[0]
```

```{code-cell} ipython3
source[1]
```

```{code-cell} ipython3
source[2]
```

اکنون می‌توانیم کد اضافی برای تجزیه این متن و ذخیره آن به عنوان یک آرایه بنویسیم.

اما این غیرضروری است --- تابع `read_csv` در pandas می‌تواند این کار را برای ما انجام دهد.

از `parse_dates=True` استفاده می‌کنیم تا pandas ستون تاریخ‌های ما را تشخیص دهد و فیلتر کردن تاریخ ساده را امکان‌پذیر کند

```{code-cell} ipython3
data = pd.read_csv(url, index_col=0, parse_dates=True)
```

داده به یک DataFrame در pandas به نام `data` خوانده شده است که اکنون می‌توانیم به روش معمول با آن کار کنیم

```{code-cell} ipython3
type(data)
```

```{code-cell} ipython3
data.head()  # یک متد مفید برای نگاه سریع به دیتافریم
```

```{code-cell} ipython3
pd.set_option('display.precision', 1)
data.describe()  # خروجی شما ممکن است کمی متفاوت باشد
```

همچنین می‌توانیم نرخ بیکاری از 2006 تا 2012 را به شرح زیر رسم کنیم

```{code-cell} ipython3
ax = data['2006':'2012'].plot(title='US Unemployment Rate', legend=False)
ax.set_xlabel('year', fontsize=12)
ax.set_ylabel('%', fontsize=12)
plt.show()
```

توجه کنید که pandas گزینه‌های بسیار دیگری برای نوع فایل ارائه می‌دهد.

Pandas [تنوع گسترده‌ای](https://pandas.pydata.org/pandas-docs/stable/user_guide/io.html) از متدهای سطح بالا دارد که می‌توانیم از آنها برای خواندن excel، json، parquet استفاده کنیم یا مستقیماً به یک سرور پایگاه داده وصل شویم.

### استفاده از {index}`wbgapi <single: wbgapi>` و {index}`yfinance <single: yfinance>` برای دسترسی به داده

کتابخانه python [wbgapi](https://pypi.org/project/wbgapi/) می‌تواند برای واکشی داده از پایگاه‌های داده متعددی که توسط بانک جهانی منتشر شده‌اند استفاده شود.

```{note}
می‌توانید اطلاعات مفیدی درباره بسته [wbgapi](https://pypi.org/project/wbgapi/) در این [پست وبلاگ بانک جهانی](https://blogs.worldbank.org/en/opendata/introducing-wbgapi-new-python-package-accessing-world-bank-data) و همچنین این [آموزش](https://github.com/tgherzog/wbgapi/blob/master/examples/wbgapi-quickstart.ipynb) پیدا کنید
```

همچنین از [yfinance](https://pypi.org/project/yfinance/) برای واکشی داده از Yahoo finance در تمرین‌ها استفاده خواهیم کرد.

فعلاً بیایید یک مثال از دانلود و رسم نمودار داده را کار کنیم --- این بار از بانک جهانی.

بانک جهانی [داده را جمع‌آوری و سازماندهی می‌کند](https://data.worldbank.org/indicator) در مجموعه‌ای عظیم از شاخص‌ها.

به عنوان مثال، [در اینجا](https://data.worldbank.org/indicator/GC.DOD.TOTL.GD.ZS) برخی داده‌ها در مورد بدهی دولت به عنوان نسبتی از تولید ناخالص داخلی آورده شده است.

مثال کد بعدی داده را برای شما واکشی می‌کند و سری‌های زمانی را برای ایالات متحده و استرالیا رسم می‌کند

```{code-cell} ipython3
import wbgapi as wb
wb.series.info('GC.DOD.TOTL.GD.ZS')
```

```{code-cell} ipython3
govt_debt = wb.data.DataFrame('GC.DOD.TOTL.GD.ZS', economy=['USA','AUS'], time=range(2005,2016))
govt_debt = govt_debt.T    # انتقال سال‌ها از ستون‌ها به سطرها برای رسم نمودار
```

```{code-cell} ipython3
govt_debt.plot(xlabel='year', ylabel='Government debt (% of GDP)');
```

## تمرین‌ها

```{exercise-start}
:label: pd_ex1
```

با این import‌ها:

```{code-cell} ipython3
import datetime as dt
import yfinance as yf
```

برنامه‌ای بنویسید تا درصد تغییر قیمت در سال 2021 را برای سهام زیر محاسبه کنید:

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

در اینجا قسمت اول برنامه آورده شده است

```{code-cell} ipython3
def read_data(ticker_list,
          start=dt.datetime(2021, 1, 1),
          end=dt.datetime(2021, 12, 31)):
    """
    This function reads in closing price data from Yahoo
    for each tick in the ticker_list.
    """
    ticker = pd.DataFrame()

    for tick in ticker_list:
        stock = yf.Ticker(tick)
        prices = stock.history(start=start, end=end)

        # Change the index to date-only
        prices.index = pd.to_datetime(prices.index.date)
        
        closing_prices = prices['Close']
        ticker[tick] = closing_prices

    return ticker

ticker = read_data(ticker_list)
```

برنامه را تکمیل کنید تا نتیجه را به صورت نمودار میله‌ای مانند این رسم کنید:

```{image} /_static/lecture_specific/pandas/pandas_share_prices.png
:scale: 80
:align: center
```

```{exercise-end}
```

```{solution-start} pd_ex1
:class: dropdown
```

چند راه برای رویکرد به این مشکل با استفاده از Pandas برای محاسبه درصد تغییر وجود دارد.

اول، می‌توانید داده را استخراج کنید و محاسبه را انجام دهید مانند:

```{code-cell} ipython3
p1 = ticker.iloc[0]    # اولین مجموعه قیمت‌ها را به عنوان یک Series بگیرید
p2 = ticker.iloc[-1]   # آخرین مجموعه قیمت‌ها را به عنوان یک Series بگیرید
price_change = (p2 - p1) / p1 * 100
price_change
```

از طرف دیگر می‌توانید از یک متد داخلی `pct_change` استفاده کنید و آن را برای انجام محاسبه صحیح با استفاده از آرگومان `periods` پیکربندی کنید.

```{code-cell} ipython3
change = ticker.pct_change(periods=len(ticker)-1, axis='rows')*100
price_change = change.iloc[-1]
price_change
```

سپس برای رسم نمودار

```{code-cell} ipython3
price_change.sort_values(inplace=True)
price_change.rename(index=ticker_list, inplace=True)
```

```{code-cell} ipython3
fig, ax = plt.subplots(figsize=(10,8))
ax.set_xlabel('stock', fontsize=12)
ax.set_ylabel('percentage change in price', fontsize=12)
price_change.plot(kind='bar', ax=ax)
plt.show()
```

```{solution-end}
```

```{exercise-start}
:label: pd_ex2
```

با استفاده از متد `read_data` معرفی شده در {ref}`pd_ex1`، برنامه‌ای بنویسید تا درصد تغییر سال به سال را برای شاخص‌های زیر به دست آورید:

```{code-cell} ipython3
indices_list = {'^GSPC': 'S&P 500',
               '^IXIC': 'NASDAQ',
               '^DJI': 'Dow Jones',
               '^N225': 'Nikkei'}
```

برنامه را تکمیل کنید تا آمار خلاصه را نشان دهد و نتیجه را به صورت نمودار سری زمانی مانند این رسم کنید:

```{image} /_static/lecture_specific/pandas/pandas_indices_pctchange.png
:scale: 80
:align: center
```

```{exercise-end}
```

```{solution-start} pd_ex2
:class: dropdown
```

با پیروی از کاری که در {ref}`pd_ex1` انجام دادید، می‌توانید داده را با استفاده از `read_data` با به‌روزرسانی تاریخ‌های شروع و پایان به طور مناسب پرس‌وجو کنید.

```{code-cell} ipython3
indices_data = read_data(
        indices_list,
        start=dt.datetime(1971, 1, 1),  # تاریخ شروع مشترک
        end=dt.datetime(2021, 12, 31)
)
```

سپس، اولین و آخرین مجموعه قیمت‌ها در هر سال را به عنوان DataFrames استخراج کنید و بازده سالانه را محاسبه کنید مانند:

```{code-cell} ipython3
yearly_returns = pd.DataFrame()

for index, name in indices_list.items():
    p1 = indices_data.groupby(indices_data.index.year)[index].first()  # اولین مجموعه بازده‌ها را به عنوان DataFrame بگیرید
    p2 = indices_data.groupby(indices_data.index.year)[index].last()   # آخرین مجموعه بازده‌ها را به عنوان DataFrame بگیرید
    returns = (p2 - p1) / p1
    yearly_returns[name] = returns

yearly_returns
```

در مرحله بعد، می‌توانید آمار خلاصه را با استفاده از متد `describe` به دست آورید.

```{code-cell} ipython3
yearly_returns.describe()
```

سپس، برای رسم نمودار

```{code-cell} ipython3
fig, axes = plt.subplots(2, 2, figsize=(10, 8))

for iter_, ax in enumerate(axes.flatten()):            # تبدیل آرایه 2 بعدی به آرایه 1 بعدی
    index_name = yearly_returns.columns[iter_]         # نام اندیس را در هر تکرار بگیرید
    ax.plot(yearly_returns[index_name])                # رسم درصد تغییر بازده سالانه در هر اندیس
    ax.set_ylabel("percent change", fontsize = 12)
    ax.set_title(index_name)

plt.tight_layout()
```

```{solution-end}
```

[^mung]: ویکی‌پدیا munging را به عنوان پاک‌سازی داده از یک فرم خام به یک فرم ساختاریافته و تصفیه شده تعریف می‌کند.