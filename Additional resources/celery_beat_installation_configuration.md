# Celery Beat Installation & Configuration (Django)

## 1. What Is Celery Beat?

**Celery Beat** is a scheduler that **adds periodic tasks to your Celery queue**.
It lets you run tasks at regular intervals (e.g., daily, hourly) or on a custom schedule without manual triggering.

---

## 2. Install Celery Beat

Install the package with pip:

```bash
pip install django-celery-beat
```

---

## 3. Add to Django Installed Apps

In `settings.py`:

```python
INSTALLED_APPS = [
    # other apps
    'django_celery_beat',
]
```

---

## 4. Run Migrations

Celery Beat stores schedules in the database, so run:

```bash
python manage.py migrate
```

---

## 5. Configure Celery Beat Scheduler

In `settings.py`, tell Celery to use the database scheduler:

```python
CELERY_BEAT_SCHEDULER = 'django_celery_beat.schedulers:DatabaseScheduler'
```

---

## 6. Creating Periodic Tasks

You can define periodic tasks in two ways:

### Option 1: Via Django Admin

1. Go to **Django Admin → Periodic Tasks**
2. Add a new **Periodic Task**
3. Select the task from your registered Celery tasks
4. Set the schedule (interval, crontab, or solar)

### Option 2: Programmatically

In Python:

```python
from django_celery_beat.models import PeriodicTask, IntervalSchedule

# Create interval schedule (every 10 minutes)
schedule, created = IntervalSchedule.objects.get_or_create(
    every=10,
    period=IntervalSchedule.MINUTES,
)

# Create periodic task
PeriodicTask.objects.create(
    interval=schedule,
    name='Send reminder emails',
    task='application.tasks.send_email_task',
    args='["user@example.com"]',
)
```

---

## 7. Run Celery Beat

From your project root:

```bash
celery -A architecture_portal beat --loglevel=info
```

This starts the scheduler, which pushes periodic tasks into the Celery queue.

---

## 8. Run Celery Worker (Required)

Ensure you also have a Celery worker running to process tasks:

```bash
celery -A architecture_portal worker --loglevel=info
```

---

## 9. Optional: Run Beat and Worker Together

You can combine both for development:

```bash
celery -A architecture_portal worker --beat --loglevel=info
```

> Note: For production, it’s better to run Beat and Worker as separate services.

---

## Summary

| Component                       | Role                            |
| ------------------------------- | ------------------------------- |
| Celery Beat                     | Scheduler for periodic tasks    |
| Django Admin / Programmatic API | Define task schedules           |
| Celery Worker                   | Executes the tasks in the queue |
| Database                        | Stores schedule information     |
