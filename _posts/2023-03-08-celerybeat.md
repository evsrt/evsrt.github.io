---
title: Celery beat
date: 2023-03-08 13:01:10 +0300
categories: [system, tools]
tags: [tools]     # TAG names should always be lowercase
#img_path: /img/
---
Celery Beat is a task scheduler built into the Celery distributed task queue library. It allows you to schedule periodic tasks, such as sending emails, generating reports, or performing regular database backups.
To use Celery Beat, you need to define the tasks you want to run as Celery tasks and then schedule them using a schedule. The schedule defines when the task should be run, such as every minute, every hour, or every day at a specific time.
Here's an example of defining a Celery task and scheduling it to run every minute using Celery Beat:
python

```python
from celery import Celery
from celery.schedules import crontab

app = Celery('myapp', broker='amqp://guest@localhost//')

@app.task
def my_task():
    print('Hello, world!')

app.conf.beat_schedule = {
    'run-every-minute': {
        'task': 'my_task',
        'schedule': crontab(),
    },
}
```
In this example, my_task is a Celery task that prints "Hello, world!" when it is run. The app.conf.beat_schedule dictionary defines the schedule for the task. The 'run-every-minute' key is a name for the schedule, which can be anything you want. The 'task' key is the name of the Celery task to run. The 'schedule' key is a crontab object, which is a way to define a schedule using a syntax similar to the Unix cron utility. In this case, crontab() creates a schedule that runs every minute.
Here's another example that schedules a task to run every day at midnight:
python
```python
from celery import Celery
from celery.schedules import crontab

app = Celery('myapp', broker='amqp://guest@localhost//')

@app.task
def my_task():
    print('Hello, world!')

app.conf.beat_schedule = {
    'run-every-day-at-midnight': {
        'task': 'my_task',
        'schedule': crontab(minute=0, hour=0),
    },
}
```
In this example, the 'schedule' key is set to crontab(minute=0, hour=0), which creates a schedule that runs every day at midnight.
Celery Beat provides many other ways to define schedules, including schedules that run at specific intervals, schedules that run on specific days of the week, and schedules that run on specific dates. You can find more information about these scheduling options in the Celery documentation.

Celery Beat is a task scheduler that is built into the Celery distributed task queue library. Some of the key features of Celery Beat include:
 1. Task scheduling: Celery Beat allows you to schedule tasks to run at specific intervals, such as every minute, every hour, or every day at a specific time. You can also schedule tasks to run on specific dates or days of the week.
 2. Crontab scheduling: Celery Beat uses a syntax similar to the Unix cron utility to define schedules. This makes it easy to create complex schedules using a familiar syntax.
 3. Timezone support: Celery Beat supports timezones, so you can schedule tasks to run in a specific timezone.
 4. Task result storage: Celery Beat stores the results of scheduled tasks, so you can see when tasks ran and whether they succeeded or failed.
 5. Dynamic scheduling: You can add and remove schedules dynamically at runtime, without having to restart the Celery worker.
 6. Configurable concurrency: Celery Beat supports concurrent execution of tasks, so you can configure how many tasks are run at the same time.
 7. Task dependencies: Celery Beat allows you to define dependencies between tasks, so you can ensure that tasks are run in the correct order.
 8. Task retries: Celery Beat can automatically retry tasks that fail, with configurable retry limits and backoff periods.
 
Overall, Celery Beat provides a flexible and powerful way to schedule periodic tasks in a distributed system. It can be used for a wide range of tasks, from sending emails to processing large amounts of data.

## advantages over crontab

Celery Beat provides several advantages over traditional crontab scheduling:
 1. Dynamic scheduling: With Celery Beat, you can add, remove, and modify schedules at runtime without having to edit the crontab file and restart the cron daemon.
 2. Distributed scheduling: Celery Beat allows you to distribute scheduled tasks across multiple workers, so you can scale your task processing horizontally.
 3. Task dependencies: Celery Beat supports task dependencies, so you can ensure that tasks are run in the correct order and that later tasks don't start until earlier tasks have completed.
 4. Task retries: Celery Beat supports automatic task retries, with configurable retry limits and backoff periods, which can help to ensure that tasks complete successfully.
 5. Timezone support: Celery Beat supports timezones, so you can schedule tasks to run in a specific timezone.
 6. Task result storage: Celery Beat stores the results of scheduled tasks, so you can see when tasks ran and whether they succeeded or failed.
 7. Advanced scheduling options: Celery Beat provides more advanced scheduling options than crontab, including schedules based on specific dates, days of the week, and intervals.

Overall, Celery Beat provides a more powerful and flexible way to schedule tasks than crontab, particularly in distributed systems where scalability and dynamic scheduling are important.