# Task Scheduler Bundle

A task scheduler for **Symfony** applications using [CRON](https://en.wikipedia.org/wiki/Cron) jobs porting that allows you to run tasks periodically.


## Requirements

For this bundle to work, you must be able to define *CRON* jobs on your server.


## Installation

Start by adding the bundle to your *composer.json* : `composer require rewieer/taskschedulerbundle`

Then add the bundle to your *AppKernel.php* :
```php
// in AppKernel::registerBundles()
$bundles = array(
    // ...
    new Rewieer\TaskSchedulerBundle\RewieerTaskSchedulerBundle(),
    // ...
);
```

You're ready to start scheduling!


## Usage

First, add the following line to your *CRON* tabs : `* * * * * php /path/to/your/project/bin/console ts:run >> /dev/null 2>&1`
>This will call the scheduler, which will review all scheduled tasks and dispatch accordingly. You can call a task directly by providing it's **ID** as a parameter to the command. Note that this **ID** is generated by the scheduler and is in no way related to the service itself.

>The `>> /dev/null 2>&1` option discards the printed results of the tasks. Removing it allows to send it by email instead. This may vary so please refer to your server's configuration.

You then need to create a task. Tasks can be any service! You just have to give it the `ts.task` tag and implement `TaskInterface`, or for simplicity extend from `AbstractScheduledTask`.

In `Foo\Bar\Task`
```php
use Rewieer\TaskSchedulerBundle\Task\AbstractScheduledTask;
use Rewieer\TaskSchedulerBundle\Task\Schedule;

class Task extends AbstractScheduledTask {
  protected function initialize(Schedule $schedule) {
    $schedule
      ->everyMinutes(5); // Perform the task every 5 minutes
  }

  public function run() {
    // Do suff
  }
}
```

In your *services.xml* :
```xml
<service id="my.task" class="Foo\Bar\Task">
  <tag name="ts.task" />
</service>
```

Your task is now scheduled and will be called every 5 minutes.


You're good to go! You can now check your logs to see if this is working.
