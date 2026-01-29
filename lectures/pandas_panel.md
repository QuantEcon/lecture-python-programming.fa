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
  overview: مروری کلی
  slicing-and-reshaping-data: برش و تغییر شکل داده‌ها
  merging-dataframes-and-filling-nans: ادغام Dataframe ها و پر کردن NaN ها
  grouping-and-summarizing-data: گروه‌بندی و خلاصه کردن داده‌ها
  final-remarks: نکات پایانی
  exercises: تمرین‌ها
---

```{raw} jupyter
<div id="qe-notebook-header" align="right" style="text-align:right;">
        <a href="https://quantecon.org/" title="quantecon.org">
                <img style="width:250px;display:inline;" width="250px" src="https://assets.quantecon.org/img/qe-menubar-logo.svg" alt="QuantEcon">
        </a>
</div>
```

(ppd)=
# {index}`Pandas برای داده‌های پانلی <single: Pandas for Panel Data>`

```{index} single: Python; Pandas
```

علاوه بر آنچه در Anaconda موجود است، این سخنرانی به کتابخانه‌های زیر نیاز دارد:

```{code-cell} ipython3
:tags: [hide-output]

!pip install --upgrade seaborn
```

ما از import های زیر استفاده می‌کنیم.

```{code-cell} ipython3
import matplotlib.pyplot as plt
import seaborn as sns
sns.set_theme()
```

## مروری کلی

در {doc}`سخنرانی پیشین درباره pandas <pandas>`، ما با مجموعه داده‌های ساده کار کردیم.

اقتصادسنجان اغلب نیاز به کار با مجموعه داده‌های پیچیده‌تر مانند پانل‌ها دارند.

وظایف رایج شامل موارد زیر است:

* وارد کردن داده‌ها، تمیز کردن و تغییر شکل دادن آنها در چندین محور.
* انتخاب یک سری زمانی یا مقطع عرضی از یک پانل.
* گروه‌بندی و خلاصه کردن داده‌ها.

`pandas` (مشتق از 'panel' و 'data') شامل ابزارهای قدرتمند و آسان برای حل دقیقاً این نوع مشکلات است.

در ادامه، ما از یک مجموعه داده پانلی از حداقل دستمزدهای واقعی از OECD برای ایجاد موارد زیر استفاده خواهیم کرد:

* آمار خلاصه در ابعاد مختلف داده‌های ما
* یک سری زمانی از میانگین حداقل دستمزد کشورها در مجموعه داده
* برآوردهای چگالی کرنل دستمزدها بر اساس قاره

ما با خواندن داده‌های پانلی فرمت طولانی خود از یک فایل CSV شروع می‌کنیم و `DataFrame` حاصل را با `pivot_table` تغییر شکل می‌دهیم تا یک `MultiIndex` بسازیم.

جزئیات اضافی با استفاده از تابع `merge` پانداز به `DataFrame` ما اضافه خواهد شد، و داده‌ها با تابع `groupby` خلاصه خواهند شد.

## برش و تغییر شکل داده‌ها

ما مجموعه داده‌ای از OECD از حداقل دستمزدهای واقعی در 32 کشور را می‌خوانیم و آن را به `realwage` اختصاص می‌دهیم.

مجموعه داده با لینک زیر قابل دسترسی است:

```{code-cell} ipython3
url1 = 'https://raw.githubusercontent.com/QuantEcon/lecture-python/master/source/_static/lecture_specific/pandas_panel/realwage.csv'
```

```{code-cell} ipython3
import pandas as pd

# Display 6 columns for viewing purposes
pd.set_option('display.max_columns', 6)

# Reduce decimal points to 2
pd.options.display.float_format = '{:,.2f}'.format

realwage = pd.read_csv(url1)
```

بیایید نگاهی به آنچه برای کار داریم بیندازیم

```{code-cell} ipython3
realwage.head()  # Show first 5 rows
```

داده‌ها در حال حاضر در فرمت طولانی هستند، که تجزیه و تحلیل آن زمانی که چندین بعد برای داده‌ها وجود دارد دشوار است.

ما از `pivot_table` برای ایجاد یک پانل با فرمت عریض، با یک `MultiIndex` برای مدیریت داده‌های چند بعدی بالاتر استفاده خواهیم کرد.

آرگومان‌های `pivot_table` باید داده‌ها (values)، index و ستون‌هایی که در dataframe نتیجه می‌خواهیم را مشخص کنند.

با ارسال یک لیست در columns، می‌توانیم یک `MultiIndex` در محور ستون خود ایجاد کنیم

```{code-cell} ipython3
realwage = realwage.pivot_table(values='value',
                                index='Time',
                                columns=['Country', 'Series', 'Pay period'])
realwage.head()
```

برای فیلتر کردن آسان‌تر داده‌های سری زمانی خود، بعداً، ما index را به یک `DateTimeIndex` تبدیل خواهیم کرد

```{code-cell} ipython3
realwage.index = pd.to_datetime(realwage.index)
type(realwage.index)
```

ستون‌ها شامل سطوح متعددی از نمایه‌سازی هستند، که به عنوان `MultiIndex` شناخته می‌شوند، با سطوحی که به صورت سلسله مراتبی مرتب شده‌اند (Country > Series > Pay period).

یک `MultiIndex` ساده‌ترین و انعطاف‌پذیرترین راه برای مدیریت داده‌های پانلی در pandas است

```{code-cell} ipython3
type(realwage.columns)
```

```{code-cell} ipython3
realwage.columns.names
```

مانند قبل، می‌توانیم کشور (سطح بالای `MultiIndex` ما) را انتخاب کنیم

```{code-cell} ipython3
realwage['United States'].head()
```

stacking و unstacking سطوح `MultiIndex` در سراسر این سخنرانی برای تغییر شکل dataframe ما به فرمتی که نیاز داریم استفاده خواهد شد.

`.stack()` پایین‌ترین سطح ستون `MultiIndex` را به index ردیف می‌چرخاند (`.unstack()` در جهت مخالف کار می‌کند - امتحان کنید)

```{code-cell} ipython3
realwage.stack(future_stack=True).head()
```

همچنین می‌توانیم یک آرگومان برای انتخاب سطحی که می‌خواهیم stack کنیم ارسال کنیم

```{code-cell} ipython3
realwage.stack(level='Country', future_stack=True).head()  # future_stack=True is required until pandas>3.0
```

استفاده از یک `DatetimeIndex` انتخاب یک دوره زمانی خاص را آسان می‌کند.

انتخاب یک سال و stacking کردن دو سطح پایین‌تر `MultiIndex` یک مقطع عرضی از داده‌های پانلی ما ایجاد می‌کند

```{code-cell} ipython3
realwage.loc['2015'].stack(level=(1, 2), future_stack=True).transpose().head() # future_stack=True is required until pandas>3.0
```

برای بقیه سخنرانی، ما با یک dataframe از حداقل دستمزدهای واقعی ساعتی در کشورها و زمان، اندازه‌گیری شده در دلار 2015 آمریکا کار خواهیم کرد.

برای ایجاد dataframe فیلتر شده خود (`realwage_f`)، می‌توانیم از متد `xs` برای انتخاب مقادیر در سطوح پایین‌تر در multiindex استفاده کنیم، در حالی که سطوح بالاتر (کشورها در این مورد) را حفظ می‌کنیم

```{code-cell} ipython3
realwage_f = realwage.xs(('Hourly', 'In 2015 constant prices at 2015 USD exchange rates'),
                         level=('Pay period', 'Series'), axis=1)
realwage_f.head()
```

## ادغام Dataframe ها و پر کردن NaN ها

مشابه پایگاه‌های داده رابطه‌ای مانند SQL، pandas متدهای داخلی برای ادغام مجموعه داده‌ها با هم دارد.

با استفاده از اطلاعات کشور از [WorldData.info](https://www.worlddata.info/downloads/)، ما قاره هر کشور را با تابع `merge` به `realwage_f` اضافه خواهیم کرد.

مجموعه داده با لینک زیر قابل دسترسی است:

```{code-cell} ipython3
url2 = 'https://raw.githubusercontent.com/QuantEcon/lecture-python/master/source/_static/lecture_specific/pandas_panel/countries.csv'
```

```{code-cell} ipython3
worlddata = pd.read_csv(url2, sep=';')
worlddata.head()
```

ابتدا، ما فقط متغیرهای country و continent را از `worlddata` انتخاب می‌کنیم و ستون را به 'Country' تغییر نام می‌دهیم

```{code-cell} ipython3
worlddata = worlddata[['Country (en)', 'Continent']]
worlddata = worlddata.rename(columns={'Country (en)': 'Country'})
worlddata.head()
```

می‌خواهیم dataframe جدید خود، `worlddata`، را با `realwage_f` ادغام کنیم.

تابع `merge` پانداز اجازه می‌دهد که dataframe ها با ردیف‌ها به هم متصل شوند.

dataframe های ما با استفاده از نام کشورها ادغام خواهند شد، که نیاز داریم از transpose `realwage_f` استفاده کنیم تا ردیف‌ها در هر دو dataframe با نام کشورها مطابقت داشته باشند

```{code-cell} ipython3
realwage_f.transpose().head()
```

می‌توانیم از left، right، inner، یا outer join برای ادغام مجموعه داده‌های خود استفاده کنیم:

* left join فقط کشورهای مجموعه داده سمت چپ را شامل می‌شود
* right join فقط کشورهای مجموعه داده سمت راست را شامل می‌شود
* outer join کشورهایی را که در مجموعه داده‌های سمت چپ و راست هستند شامل می‌شود
* inner join فقط کشورهای مشترک بین مجموعه داده‌های سمت چپ و راست را شامل می‌شود

به طور پیش‌فرض، `merge` از یک inner join استفاده می‌کند.

در اینجا ما `how='left'` را ارسال خواهیم کرد تا همه کشورها در `realwage_f` را نگه داریم، اما کشورهایی در `worlddata` را که ورودی داده مربوطه در `realwage_f` ندارند دور بیندازیم.

این با سایه قرمز در نمودار زیر نشان داده شده است

```{figure} /_static/lecture_specific/pandas_panel/venn_diag.png
```

همچنین باید مشخص کنیم که نام کشور در هر dataframe کجا قرار دارد، که `key` خواهد بود که برای ادغام dataframe ها 'on' آن استفاده می‌شود.

dataframe 'سمت چپ' ما (`realwage_f.transpose()`) شامل کشورها در index است، بنابراین `left_index=True` را تنظیم می‌کنیم.

dataframe 'سمت راست' ما (`worlddata`) شامل کشورها در ستون 'Country' است، بنابراین `right_on='Country'` را تنظیم می‌کنیم

```{code-cell} ipython3
merged = pd.merge(realwage_f.transpose(), worlddata,
                  how='left', left_index=True, right_on='Country')
merged.head()
```

کشورهایی که در `realwage_f` ظاهر شدند اما در `worlddata` نبودند `NaN` در ستون Continent خواهند داشت.

برای بررسی اینکه آیا این اتفاق افتاده است، می‌توانیم از `.isnull()` در ستون continent استفاده کنیم و dataframe ادغام شده را فیلتر کنیم

```{code-cell} ipython3
merged[merged['Continent'].isnull()]
```

ما سه مقدار گمشده داریم!

یک گزینه برای مقابله با مقادیر NaN ایجاد یک dictionary حاوی این کشورها و قاره‌های مربوطه آنها است.

`.map()` کشورهای موجود در `merged['Country']` را با قاره آنها از dictionary مطابقت می‌دهد.

توجه کنید که کشورهای موجود در dictionary ما با `NaN` نگاشت می‌شوند

```{code-cell} ipython3
missing_continents = {'Korea': 'Asia',
                      'Russian Federation': 'Europe',
                      'Slovak Republic': 'Europe'}

merged['Country'].map(missing_continents)
```

نمی‌خواهیم کل سری را با این نگاشت بازنویسی کنیم.

`.fillna()` فقط مقادیر `NaN` در `merged['Continent']` را با نگاشت پر می‌کند، در حالی که سایر مقادیر در ستون بدون تغییر باقی می‌مانند

```{code-cell} ipython3
merged['Continent'] = merged['Continent'].fillna(merged['Country'].map(missing_continents))

# Check for whether continents were correctly mapped

merged[merged['Country'] == 'Korea']
```

همچنین قاره آمریکا را در یک قاره واحد ترکیب خواهیم کرد - این کار تجسم ما را بعداً زیباتر خواهد کرد.

برای انجام این کار، از `.replace()` استفاده خواهیم کرد و از طریق لیستی از مقادیر قاره‌ای که می‌خواهیم جایگزین کنیم حلقه می‌زنیم

```{code-cell} ipython3
replace = ['Central America', 'North America', 'South America']
merged['Continent'] = merged['Continent'].replace(to_replace=replace, value='America')
```

اکنون که همه داده‌هایی که می‌خواهیم در یک `DataFrame` واحد داریم، آن را به فرم پانلی با یک `MultiIndex` تغییر شکل خواهیم داد.

همچنین باید مطمئن شویم که index را با استفاده از `.sort_index()` مرتب کنیم تا بتوانیم بعداً به طور کارآمد dataframe خود را فیلتر کنیم.

به طور پیش‌فرض، سطوح از بالا به پایین مرتب خواهند شد

```{code-cell} ipython3
merged = merged.set_index(['Continent', 'Country']).sort_index()
merged.head()
```

در حین ادغام، `DatetimeIndex` خود را از دست دادیم، زیرا ستون‌هایی را که در فرمت datetime نبودند ادغام کردیم

```{code-cell} ipython3
merged.columns
```

اکنون که ستون‌های ادغام شده را به عنوان index تنظیم کرده‌ایم، می‌توانیم یک `DatetimeIndex` با استفاده از `.to_datetime()` دوباره ایجاد کنیم

```{code-cell} ipython3
merged.columns = pd.to_datetime(merged.columns)
merged.columns = merged.columns.rename('Time')
merged.columns
```

`DatetimeIndex` معمولاً در محور ردیف روان‌تر کار می‌کند، بنابراین ما پیش خواهیم رفت و `merged` را transpose خواهیم کرد

```{code-cell} ipython3
merged = merged.transpose()
merged.head()
```

## گروه‌بندی و خلاصه کردن داده‌ها

گروه‌بندی و خلاصه کردن داده‌ها می‌تواند به ویژه برای درک مجموعه داده‌های پانلی بزرگ مفید باشد.

یک روش ساده برای خلاصه کردن داده‌ها فراخوانی یک [متد تجمیعی](https://pandas.pydata.org/pandas-docs/stable/getting_started/intro_tutorials/06_calculate_statistics.html) در dataframe است، مانند `.mean()` یا `.max()`.

به عنوان مثال، می‌توانیم میانگین حداقل دستمزد واقعی را برای هر کشور در دوره 2006 تا 2016 محاسبه کنیم (پیش‌فرض تجمیع بر روی ردیف‌ها است)

```{code-cell} ipython3
merged.mean().head(10)
```

با استفاده از این سری، می‌توانیم میانگین حداقل دستمزد واقعی را در طول دهه گذشته برای هر کشور در مجموعه داده خود رسم کنیم

```{code-cell} ipython3
merged.mean().sort_values(ascending=False).plot(kind='bar',
                                                title="Average real minimum wage 2006 - 2016")

# Set country labels
country_labels = merged.mean().sort_values(ascending=False).index.get_level_values('Country').tolist()
plt.xticks(range(0, len(country_labels)), country_labels)
plt.xlabel('Country')

plt.show()
```

ارسال `axis=1` به `.mean()` بر روی ستون‌ها تجمیع خواهد کرد (میانگین حداقل دستمزد برای همه کشورها در طول زمان را می‌دهد)

```{code-cell} ipython3
merged.mean(axis=1).head()
```

می‌توانیم این سری زمانی را به صورت یک نمودار خطی رسم کنیم

```{code-cell} ipython3
merged.mean(axis=1).plot()
plt.title('Average real minimum wage 2006 - 2016')
plt.ylabel('2015 USD')
plt.xlabel('Year')
plt.show()
```

همچنین می‌توانیم یک سطح از `MultiIndex` (در محور ستون) را برای تجمیع بر روی آن مشخص کنیم.

در مورد `groupby` ما نیاز داریم از `.T` برای transpose کردن ستون‌ها به ردیف‌ها استفاده کنیم زیرا `pandas` استفاده از `axis=1` را در متد `groupby` منسوخ کرده است.

```{code-cell} ipython3
merged.T.groupby(level='Continent').mean().head()
```

می‌توانیم میانگین حداقل دستمزدها در هر قاره را به عنوان یک سری زمانی رسم کنیم

```{code-cell} ipython3
merged.T.groupby(level='Continent').mean().T.plot()
plt.title('Average real minimum wage')
plt.ylabel('2015 USD')
plt.xlabel('Year')
plt.show()
```

استرالیا را به عنوان یک قاره برای اهداف رسم حذف خواهیم کرد

```{code-cell} ipython3
merged = merged.drop('Australia', level='Continent', axis=1)
merged.T.groupby(level='Continent').mean().T.plot()
plt.title('Average real minimum wage')
plt.ylabel('2015 USD')
plt.xlabel('Year')
plt.show()
```

`.describe()` برای بازیابی سریع تعدادی از آمار خلاصه رایج مفید است

```{code-cell} ipython3
merged.stack(future_stack=True).describe()
```

این یک روش ساده شده برای استفاده از `groupby` است.

استفاده از `groupby` به طور کلی از یک فرایند 'تقسیم-اعمال-ترکیب' پیروی می‌کند:

* split: داده‌ها بر اساس یک یا چند کلید گروه‌بندی می‌شوند
* apply: یک تابع به صورت مستقل روی هر گروه فراخوانی می‌شود
* combine: نتایج فراخوانی‌های تابع در یک ساختار داده جدید ترکیب می‌شوند

متد `groupby` اولین مرحله از این فرایند را انجام می‌دهد، یک شیء `DataFrameGroupBy` جدید با داده‌های تقسیم شده به گروه‌ها ایجاد می‌کند.

بیایید `merged` را دوباره بر اساس قاره تقسیم کنیم، این بار با استفاده از تابع `groupby`، و شیء حاصل را `grouped` نامگذاری کنیم

```{code-cell} ipython3
grouped = merged.T.groupby(level='Continent')
grouped
```

فراخوانی یک متد تجمیعی روی شیء، تابع را به هر گروه اعمال می‌کند، که نتایج آن در یک ساختار داده جدید ترکیب می‌شوند.

به عنوان مثال، می‌توانیم تعداد کشورها در مجموعه داده خود را برای هر قاره با استفاده از `.size()` برگردانیم.

در این مورد، ساختار داده جدید ما یک `Series` است

```{code-cell} ipython3
grouped.size()
```

با فراخوانی `.get_group()` برای برگرداندن فقط کشورها در یک گروه واحد، می‌توانیم یک برآورد چگالی کرنل از توزیع حداقل دستمزدهای واقعی در 2016 برای هر قاره ایجاد کنیم.

`grouped.groups.keys()` کلیدها را از شیء `groupby` برمی‌گرداند

```{code-cell} ipython3
continents = grouped.groups.keys()

for continent in continents:
    sns.kdeplot(grouped.get_group(continent).T.loc['2015'].unstack(), label=continent, fill=True)

plt.title('Real minimum wages in 2015')
plt.xlabel('US dollars')
plt.legend()
plt.show()
```

## نکات پایانی

این سخنرانی مقدمه‌ای بر برخی از ویژگی‌های پیشرفته‌تر pandas، از جمله multiindex ها، ادغام، گروه‌بندی و رسم نمودار ارائه کرده است.

سایر ابزارهایی که ممکن است در تجزیه و تحلیل داده‌های پانلی مفید باشند شامل [xarray](https://docs.xarray.dev/en/stable/) می‌شوند، یک بسته پایتون که pandas را به ساختارهای داده N بعدی گسترش می‌دهد.

## تمرین‌ها

```{exercise-start}
:label: pp_ex1
```

در این تمرین‌ها، شما با یک مجموعه داده از نرخ‌های اشتغال در اروپا بر اساس سن و جنسیت از [Eurostat](https://ec.europa.eu/eurostat/data/database) کار خواهید کرد.

مجموعه داده با لینک زیر قابل دسترسی است:

```{code-cell} ipython3
url3 = 'https://raw.githubusercontent.com/QuantEcon/lecture-python/master/source/_static/lecture_specific/pandas_panel/employ.csv'
```

خواندن فایل CSV یک مجموعه داده پانلی در فرمت طولانی را برمی‌گرداند. از `.pivot_table()` برای ساخت یک dataframe با فرمت عریض با یک `MultiIndex` در ستون‌ها استفاده کنید.

با کاوش در dataframe و متغیرهای موجود در سطوح `MultiIndex` شروع کنید.

برنامه‌ای بنویسید که به سرعت همه مقادیر در `MultiIndex` را برمی‌گرداند.

```{exercise-end}
```

```{solution-start} pp_ex1
:class: dropdown
```

```{code-cell} ipython3
employ = pd.read_csv(url3)
employ = employ.pivot_table(values='Value',
                            index=['DATE'],
                            columns=['UNIT','AGE', 'SEX', 'INDIC_EM', 'GEO'])
employ.index = pd.to_datetime(employ.index) # ensure that dates are datetime format
employ.head()
```

این یک مجموعه داده بزرگ است بنابراین کاوش در سطوح و متغیرهای موجود مفید است

```{code-cell} ipython3
employ.columns.names
```

متغیرهای درون سطوح می‌توانند به سرعت با یک حلقه بازیابی شوند

```{code-cell} ipython3
for name in employ.columns.names:
    print(name, employ.columns.get_level_values(name).unique())
```

```{solution-end}
```

```{exercise-start}
:label: pp_ex2
```

dataframe بالا را فیلتر کنید تا فقط اشتغال را به عنوان درصدی از 'جمعیت فعال' شامل شود.

یک boxplot گروه‌بندی شده با استفاده از `seaborn` از نرخ‌های اشتغال در 2015 بر اساس گروه سنی و جنسیت ایجاد کنید.

```{hint}
:class: dropdown

`GEO` هم مناطق و هم کشورها را شامل می‌شود.
```

```{exercise-end}
```

```{solution-start} pp_ex2
:class: dropdown
```

برای فیلتر کردن آسان بر اساس کشور، `GEO` را به سطح بالا swap کنید و `MultiIndex` را مرتب کنید

```{code-cell} ipython3
employ.columns = employ.columns.swaplevel(0,-1)
employ = employ.sort_index(axis=1)
```

باید چند مورد در `GEO` که کشور نیستند را حذف کنیم.

یک روش سریع برای خلاص شدن از مناطق EU استفاده از list comprehension برای یافتن مقادیر سطح در `GEO` است که با 'Euro' شروع می‌شوند

```{code-cell} ipython3
geo_list = employ.columns.get_level_values('GEO').unique().tolist()
countries = [x for x in geo_list if not x.startswith('Euro')]
employ = employ[countries]
employ.columns.get_level_values('GEO').unique()
```

فقط درصد اشتغال در جمعیت فعال را از dataframe انتخاب کنید

```{code-cell} ipython3
employ_f = employ.xs(('Percentage of total population', 'Active population'),
                     level=('UNIT', 'INDIC_EM'),
                     axis=1)
employ_f.head()
```

مقدار 'Total' را قبل از ایجاد boxplot گروه‌بندی شده حذف کنید

```{code-cell} ipython3
employ_f = employ_f.drop('Total', level='SEX', axis=1)
```

```{code-cell} ipython3
box = employ_f.loc['2015'].unstack().reset_index()
sns.boxplot(x="AGE", y=0, hue="SEX", data=box, palette=("husl"), showfliers=False)
plt.xlabel('')
plt.xticks(rotation=35)
plt.ylabel('Percentage of population (%)')
plt.title('Employment in Europe (2015)')
plt.legend(bbox_to_anchor=(1,0.5))
plt.show()
```

```{solution-end}
```