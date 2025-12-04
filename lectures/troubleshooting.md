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
  fixing-your-local-environment: اصلاح محیط محلی شما
  reporting-an-issue: گزارش یک مشکل
---

(troubleshooting)=
```{raw} jupyter
<div id="qe-notebook-header" align="right" style="text-align:right;">
        <a href="https://quantecon.org/" title="quantecon.org">
                <img style="width:250px;display:inline;" width="250px" src="https://assets.quantecon.org/img/qe-menubar-logo.svg" alt="QuantEcon">
        </a>
</div>
```

# عیب‌یابی

این صفحه برای خوانندگانی است که هنگام اجرای کدهای موجود در سخنرانی‌ها با خطا مواجه می‌شوند.

## اصلاح محیط محلی شما

فرض اولیه سخنرانی‌ها این است که کد موجود در یک سخنرانی باید هر زمان اجرا شود که:

1. در یک Jupyter notebook اجرا شود و
1. notebook روی دستگاهی با آخرین نسخه Anaconda Python در حال اجرا باشد.

شما Anaconda را نصب کرده‌اید، درست است؟ طبق دستورالعمل‌های موجود در {doc}`این سخنرانی <getting_started>`؟

با فرض اینکه نصب کرده‌اید، رایج‌ترین منبع مشکلات برای خوانندگان ما این است که توزیع Anaconda آن‌ها به‌روز نیست.

[این مقاله مفید است](https://www.anaconda.com/blog/keeping-anaconda-date)
درباره چگونگی به‌روزرسانی Anaconda.

گزینه دیگر این است که به سادگی Anaconda را حذف کرده و دوباره نصب کنید.

همچنین نیاز دارید که کتابخانه‌های کد خارجی مانند [QuantEcon.py](https://quantecon.org/quantecon-py/) را به‌روز نگه دارید.

برای این کار می‌توانید:

* از conda upgrade quantecon در خط فرمان استفاده کنید، یا
* !conda upgrade quantecon را در یک Jupyter notebook اجرا کنید.

اگر محیط محلی شما هنوز کار نمی‌کند، می‌توانید دو کار انجام دهید.

اول، می‌توانید به جای آن از یک دستگاه راه دور استفاده کنید، با کلیک روی آیکون Launch Notebook که برای هر سخنرانی موجود است

```{image} _static/lecture_specific/troubleshooting/launch.png

```

دوم، می‌توانید یک مشکل را گزارش دهید تا ما بتوانیم سعی کنیم تنظیمات محلی شما را اصلاح کنیم.

ما دوست داریم بازخورد درباره سخنرانی‌ها دریافت کنیم، بنابراین لطفاً در تماس با ما تردید نکنید.

## گزارش یک مشکل

یکی از راه‌های ارائه بازخورد، مطرح کردن یک مشکل از طریق [issue tracker](https://github.com/QuantEcon/lecture-python-programming/issues) ماست.

لطفاً تا حد امکان دقیق باشید. به ما بگویید مشکل کجاست و تا جایی که می‌توانید جزئیات در مورد تنظیمات محلی خود ارائه دهید.

در نهایت، می‌توانید بازخورد مستقیم به [contact@quantecon.org](mailto:contact@quantecon.org) ارائه دهید