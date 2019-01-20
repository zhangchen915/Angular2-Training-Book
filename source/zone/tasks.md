#  任务

So far, we have discussed how zones are created, forked and how they maintain their state across asynchronous operations. Zones are nested at runtime in the same way that execution stack frames are nested (but usually with lower depth.) There is one zone which is special in the context of nesting, and it is the top most zone. When that zone exists the execution transitions from JavaScript to native host environment. (Another way of saying it is that when the top most execution stack frame exits the control is returned to the native code.) We will call topmost zone a Task.

A task is special because knowing when a task is about to exit is useful in these circumstances:

- Frameworks  know when to render the UI.
- Measuring enter/leave allows knowing total script/task time.
- Exiting returning execution to native code allows rendering or doing I/O operations. (Knowing when it is about to happen allows for transactional behavior, ie doing things before rendering and or I/O.)

Corollary to knowing when the task enters/leaves, is to know when an API call creates a Task which will/may execute in the future.

- Test frameworks can enforce sync tests by throwing on task creation.
- Test frameworks can auto detect async tests and wait for its completion by waiting until all of the scheduled tasks have been executed.
- Long stack traces across async boundaries, by tracking task scheduling.
- Tracking user perceived actions by tracking when async tasks triggered by user complete.

There are three kinds of tasks of interest:

1. MicroTask: A microtask is work which will execute as soon as possible on empty stack frame. A microtask is guaranteed to run before host environment performs rendering or I/O operations. A microtask queue must be empty before another MacroTask or EventTask runs. (i.e. Promise.then() executes in microtask)
2. MacroTask: Macro tasks are interleaved with rendering and I/O operations of the host environment. (ie setTimeout, setInterval, etc..) Macro tasks are guaranteed to run at least once or canceled (some can run repeatedly such as setInterval). Macro tasks have an implied execution order.
3. EventTask: Event tasks are similar to macro tasks, but unlike macro tasks they may never run. When an EventTask is run, it pre-empts whatever the next task is the macro task queue. Event tasks do not create a queue. (i.e. user click, mousemove, XHR state change.)

| Type      | Scheduled                                | Execution                                |
| --------- | ---------------------------------------- | ---------------------------------------- |
| MicroTask | Microtasks are scheduled by promisses whenever they need to invoke the thenCallback.promise.then(thenCallback). | The execution of the thenCallback runs inside a microtask. Once the microtask is scheduled it can not be canceled and it is guaranteed to run exactly once. |
| MacroTask | Macro tasks are scheduled by user code using explicit API such as setTimeout(callback), setInterval(callback), etc.. | The execution of the callback runs inside a macrotask after any rendering and I/O operations has completed. Once a macro task is completed the microtask queue is drained before the execution is handed to the host environment for more rendering and I/O operations. |
| EventTask | Event tasks are scheduled using addEventListener('click', eventCallback), or similar mechanisms. | The execution of the event task may never happen, come at an unpredictable time, and can occur more than once, therefore there is no way to know how many times it will be executed. |

Why is this useful:

- Knowing when a task has executed and a microtask queue is empty allows frameworks to know when it is time to render the UI.
- Enforcing that no tasks are scheduled allows test frameworks to ensure that the test is synchronous (and hence fast and non-flaky).
- Tracking when all scheduled tasks are executed allows a test framework to know when an asynchronous test has completed.
- Tracking tasks which originated from a user action and waiting until all scheduled tasks are executed allows application to track perceived user latency of that action.
- Knowing when all outstanding tasks are executed allows end-to-end test frameworks to know how long to wait before asserting expectations and proceeding with the next step. (This makes the end-to-end tests faster, and reduces flakiness.)

Another way to think about the need for tasks is that without task the above behaviors are not possible to implement.