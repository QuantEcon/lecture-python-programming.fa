---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
translation:
  title: آمار اجرا
---

# آمار اجرا

این جدول شامل آخرین آمار اجرا است.

```{nb-exec-table}
```

(status:machine-details)=

این سخنرانی‌ها بر روی نمونه‌های `linux` از طریق `github actions` ساخته می‌شوند.

این سخنرانی‌ها از نسخه python زیر استفاده می‌کنند

```{code-cell} ipython
!python --version
```

و نسخه‌های بسته زیر

```{code-cell} ipython
:tags: [hide-output]
!conda list
```

این مجموعه سخنرانی‌ها به GPU زیر دسترسی دارد

```{code-cell} ipython
!nvidia-smi
```

می‌توانید backend مورد استفاده توسط JAX را با استفاده از دستور زیر بررسی کنید:

```{code-cell} ipython3
import jax
# Check if JAX is using GPU
print(f"JAX backend: {jax.devices()[0].platform}")
```