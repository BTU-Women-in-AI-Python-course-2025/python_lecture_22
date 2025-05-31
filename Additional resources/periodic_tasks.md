# Django Celery – Periodic Tasks with Celery Beat

## What Are Periodic Tasks?

**Periodic tasks** are tasks that run on a schedule — like cron jobs — using **Celery Beat**, a scheduler that kicks off tasks at regular intervals.

Common use cases:

* Sending weekly emails
* Cleaning up expired sessions
* Generating daily reports

---

## Requirements

Make sure `celery` and `django-celery-beat` are installed:

```bash
pip install django-celery-beat
```

Add it to `INSTALLED_APPS`:

```python
# settings.py
INSTALLED_APPS = [
    ...
    'django_celery_beat',
]
```

Run migrations:

```bash
python manage.py migrate django_celery_beat
```

---

## 🛠 Configure Beat Scheduler

In your `settings.py`, set:

```python
CELERY_BEAT_SCHEDULER = "django_celery_beat.schedulers:DatabaseScheduler"
```

This tells Celery Beat to read schedules from the database.

---

## ✅ Registering Periodic Tasks via Django Admin

1. Go to **Django Admin Panel**
2. You’ll see **Periodic Tasks** and **Interval / Crontab schedules**
3. Add a new task:

   * **Task name**: `your_app.tasks.auto_close_old_orders`
   * **Interval / Crontab**: e.g., every 1 day

> ✅ Make sure the task function is already registered using `@shared_task`.

---

## Example: Run Daily Task to Close Old Orders

Your task function (from earlier):

```python
@shared_task
def auto_close_old_orders():
    ...
```

Now in Django Admin:

* **Task name**: `your_app.tasks.auto_close_old_orders`
* **Crontab**: every day at midnight

---

## Optional: Seed Periodic Tasks Programmatically

You can also register periodic tasks in a `ready()` method or management command:

```python
from django_celery_beat.models import PeriodicTask, IntervalSchedule
import json

def create_periodic_task():
    schedule, _ = IntervalSchedule.objects.get_or_create(
        every=1,
        period=IntervalSchedule.DAYS,
    )

    PeriodicTask.objects.create(
        interval=schedule,
        name='Close old orders daily',
        task='your_app.tasks.auto_close_old_orders',
    )
```
