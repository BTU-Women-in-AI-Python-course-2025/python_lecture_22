# Periodic vs Dynamic Tasks in Celery

## What’s the Difference?

In Celery, tasks can be scheduled in two main ways:

1. **Periodic Tasks** – Run on a **recurring, fixed schedule**.
2. **Dynamic Tasks** – Scheduled **on the fly**, based on real-time conditions.

Both have their purpose. Let’s explore them in detail.

---

## 1. Periodic Tasks

These are **predefined**, recurring tasks. You configure them **once**, and they keep running at regular intervals.

📦 Typically defined using `django-celery-beat`.

### Use Cases

* Send daily newsletters
* Sync data every hour
* Run weekly reports

### Example (with `django-celery-beat`)

```python
# tasks.py
@shared_task
def send_newsletter():
    # logic here
    pass
```

You’d then set the schedule using the Django admin (under Periodic Tasks).

---

## ⚡ 2. Dynamic Tasks

These are **programmatically scheduled** tasks. You decide when and how they should run based on user actions or events.

### Use Cases

* Send email 1 hour after registration
* Cancel order if payment not received in 10 minutes
* Trigger reminders based on custom inputs

### Example

```python
from datetime import timedelta
from django.utils import timezone
from myapp.tasks import send_reminder_email

send_reminder_email.apply_async(
    args=['user@example.com'],
    eta=timezone.now() + timedelta(minutes=10)
)
```

---

## 📊 Comparison Table

| Feature             | Dynamic Task                   | Periodic Task (`django-celery-beat`) |
| ------------------- | ------------------------------ | ------------------------------------ |
| Scheduled in code?  | ✅                              | ❌ (uses DB/admin UI)                 |
| Flexible timing     | ✅                              | ❌ (fixed intervals)                  |
| Triggered by events | ✅                              | ❌                                    |
| Recurring support   | ❌ (manual rescheduling needed) | ✅                                    |
| Best for            | On-demand, user-driven tasks   | Recurring, fixed-interval jobs       |

---

## 🧰 Summary

* Use **Periodic Tasks** when your task must repeat on a known schedule.
* Use **Dynamic Tasks** when the schedule depends on **runtime logic** or **user behavior**.

