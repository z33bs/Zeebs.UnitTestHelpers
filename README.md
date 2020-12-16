# ![Logo](art/Zeebs.UnitTestHelpers-64x64.png) Unit Test Helpers
A collection of Helpers that make unit testing easier / more effective. Note, example code uses Xunit mostly.

[![Coverage](https://raw.githubusercontent.com/z33bs/Zeebs.UnitTestHelpers/develop/coverage/badge_linecoverage.svg)](https://htmlpreview.github.io/?https://raw.githubusercontent.com/z33bs/Zeebs.UnitTestHelpers/develop/coverage/index.html) [![NuGet](https://buildstats.info/nuget/Zeebs.UnitTestHelpers?includePreReleases=false)](https://www.nuget.org/packages/Zeebs.UnitTestHelpers/)

**Contents**

* [Deterministic Task Scheduler](#Deterministic-Task-Scheduler)

## Deterministic Task Scheduler
To test async code.

From [Sven Grand's Article](https://docs.microsoft.com/en-us/archive/msdn-magazine/2014/november/async-programming-unit-testing-asynchronous-code-three-solutions-for-better-tests). Solution is an alternative to simply adding the line `Thread.Sleep(WAITING_TIME_FOR_ASYNC_MS);` before Assert.

This solution allows you to step through the tasks programatically (deterministically) in your test code. Not that this requres the tested method to accept a TaskScheduler so that you can test with this user-specified scheduler. Example of use is:
```c#
[Theory]
[InlineData(500)]
[InlineData(0)]
public void AsyncCommand_ExecuteAsync_IntParameter_Test(int parameter)
{
    //Arrange
    var dts = new DeterministicTaskScheduler();

    ICommand command = new SafeCommand<int>(IntParameterTask,dts,null,null);

    //Act
    command.Execute(parameter);
    dts.RunTasksUntilIdle();

    //Assert

}

```

The method overload looks like:
```c#
/// <summary>
/// For Unit Testing. Command runs using the specified <see cref="TaskScheduler"/>
/// </summary>
[EditorBrowsable(EditorBrowsableState.Never)]
public SafeCommand(
    Func<T, Task> executeFunction,
    TaskScheduler scheduler,
    IViewModelBase viewModel = null,
    Action<Exception> onException = null,
    Func<T, bool> canExecute = null,
    //bool mustRunOnCurrentSyncContext is moot
    bool isBlocking = true
    )
```

