# How to Use Loguru for Simpler Python Logging

## Installing Loguru

Loguru is available on PyPi, and you can install it with `pip`. Open a terminal or command prompt, create a new virtual environment, and then install the library:

```bash
$ python -m venv venv/
$ source venv/bin/activate
(venv) $ python -m pip install loguru
```

This command will install the latest version of Loguru from Python Package Index (PyPI) onto your machine.

### Verifying the Installation

To verify that the installation was successful, start a Python REPL:

```bash
(venv) $ python
```

Next, import Loguru:

```python
>>> import loguru
```

If the import runs without error, then you’ve successfully installed Loguru and can now use it to log messages in your Python programs and applications.

### Understanding Basic Setup Considerations

Before diving into Loguru’s features, there are a few key points to keep in mind:

1. **Single Logger Instance**: Unlike Python’s built-in `logging` module, Loguru uses a single logger instance. You don’t need to create multiple loggers, just import the pre-configured `logger` object:

   ```python
   from loguru import logger
   ```

2. **Default Configuration**: Out of the box, Loguru logs to stderr with a reasonable default format. This means you can start logging immediately without any setup.

3. **Python Version Compatibility**: Loguru supports Python 3.5 and above.

Now that you understand these basic considerations, you’re ready to start logging with Loguru. In the next section, you’ll learn about basic logging operations and how to customize them to suit your needs.

## Learning the Fundamentals of Logging With Loguru

One of Loguru’s main attractions is its simplicity compared to Python’s built-in `logging` module. Instead of writing multiple lines of configuration code, you can start logging immediately with a pre-configured logger.

This section will cover the fundamentals of using Loguru, from basic logging to more advanced features like custom formatting and log file management. You’ll learn how to implement effective logging with minimal setup while maintaining the flexibility to customize your logging as needed.

### Getting Started With Zero Configuration

The most basic way to use Loguru is through its pre-configured `logger` object. Unlike Python’s built-in `logging` module, which requires explicit configuration before use, Loguru comes ready to use right after you import it.

You can start logging messages immediately with several logging methods that correspond to different severity levels:

```python
>>> from loguru import logger

>>> logger.debug("Debug message")
2025-02-01 11:38:50.215 | DEBUG    | __main__:<module>:1 - Debug message

>>> logger.info("Info message")
2025-02-01 11:38:50.216 | INFO     | __main__:<module>:1 - Info message

>>> logger.error("Error message")
2025-02-01 11:38:51.420 | ERROR    | __main__:<module>:1 - Error message
```

When you run this code, you’ll see that each message is automatically formatted with useful information like the timestamp, log level, and the location of the log call:

[![Loguru Basic Usage](https://files.realpython.com/media/Loguru_basic_usage.85e272875c5f.png)](https://files.realpython.com/media/Loguru_basic_usage.85e272875c5f.png)

The default output includes color-coded levels when logging to a terminal, making it possible to quickly identify different types of messages. Loguru automatically detects if your terminal supports colors and disables them if it doesn’t.

Now it’s time to take a closer look at the first logging message:

```
2025-02-01 11:38:50.215 | DEBUG    | __main__:<module>:1 - Debug message
```

In the above message, you have the following information:

- `2025-02-01 11:38:50.215`: Timestamp with millisecond precision
- `DEBUG`: Log level indicating message severity
- `__main__`: The module name where the log was called
- `<module>`: The function or scope—in this case, module level
- `1`: Line number in the source code
- `Debug message`: The actual message being logged

Each component is separated by a special character, such as a pipe (`|`) or a dash (`-`), for readability, and you can customize this format, as you’ll learn later in this tutorial.

Logs are sent to the **standard error stream** (`stderr`), which is the output channel that Loguru uses by default. It’s traditionally used for error messages and diagnostic output, making it ideal for logging since it can be separated from your program’s regular output. You can change this later when you need more complex logging setups. For example, you can send logs to files, network services, or multiple destinations at once.

Now that you understand the basics of logging with Loguru, next, you’ll explore how to categorize your messages by importance. Loguru provides several log levels that let you distinguish between different types of information, from detailed debugging output to critical errors.

### Understanding Log Levels

Now that you’re familiar with basic logging, you’ll explore Loguru’s log levels in more detail. Log levels help you categorize messages based on their severity and importance, allowing you to filter and manage your logs effectively.

Loguru provides seven built-in log levels, each with its own method, severity value, and default color scheme:

| Level      | Method              | Value | Purpose                                                      |
| ---------- | ------------------- | ----- | ------------------------------------------------------------ |
| `TRACE`    | `logger.trace()`    | 5     | Extremely detailed information for debugging                 |
| `DEBUG`    | `logger.debug()`    | 10    | Information useful during development                        |
| `INFO`     | `logger.info()`     | 20    | General information about what’s happening in the code       |
| `SUCCESS`  | `logger.success()`  | 25    | Notifications of successful operations                       |
| `WARNING`  | `logger.warning()`  | 30    | Warnings about something unexpected but not necessarily problematic |
| `ERROR`    | `logger.error()`    | 40    | Errors for when something fails but the application continues running |
| `CRITICAL` | `logger.critical()` | 50    | Critical errors that are serious and urgent                  |

By default, Loguru shows all messages with level `DEBUG` (10) and above. This means that when you first start using Loguru, you’ll see most log messages except for the extremely detailed `TRACE` messages. This behavior helps keep your logs focused on information that’s most relevant during development while filtering out excessive details. You can see this filtering in action with the following example:

```python
>>> from loguru import logger

>>> logger.trace("This is a detailed trace message!")
>>> logger.debug("Debug message!")
2025-02-02 11:37:39.443 | DEBUG    | __main__:<module>:1 - Debug message!

>>> logger.success("Success message!")
2025-02-02 11:37:39.444 | SUCCESS  | __main__:<module>:1 - Success message!

>>> logger.critical("Critical error has occurred!")
2025-02-02 11:37:39.626 | CRITICAL | __main__:<module>:1 - Critical error has occurred!
```

You’ll notice that the `TRACE` message doesn’t appear in the output, while all other levels are displayed with their appropriate formatting and colors:

[![Loguru Log Levels](https://files.realpython.com/media/Loguru_Log_Levels.c38c3c484b32.png)](https://files.realpython.com/media/Loguru_Log_Levels.c38c3c484b32.png)

The reason `TRACE` doesn’t appear in the output is because the `TRACE` level is below Loguru’s default minimum level of `DEBUG`.

To enable `TRACE` level messages, you can adjust the minimum level using the `logger.add()` method:

```python
>>> import sys
>>> from loguru import logger

>>> logger.add(sys.stderr, level="TRACE")
1

>>> logger.trace("Will this be visible?")
2025-02-03 10:24:31.720 | TRACE    | __main__:<module>:1 - Will this be visible?

>>> logger.debug("A debug message")
2025-02-03 10:24:31.721 | DEBUG    | __main__:<module>:1 - A debug message
2025-02-03 10:24:31.721 | DEBUG    | __main__:<module>:1 - A debug message

>>> logger.info("An info message")
2025-02-03 10:24:31.841 | INFO     | __main__:<module>:1 - An info message
2025-02-03 10:24:31.841 | INFO     | __main__:<module>:1 - An info message
```

In the code above, you import the `sys` module to access the standard error stream—`sys.stderr`—which will be used as a **sink** or output destination for your logs. You then use the `logger.add()` method to add a new **handler**. Here, you pass it `sys.stderr` and set its **default level** to `TRACE` using the `level` argument.

The `.add()` method returns an integer that uniquely identifies the handler. You can use this identifier to remove the handler later with `.remove()`.

While running the code, you may notice something unexpected. The `DEBUG` and `INFO`log messages appear twice. This happens because Loguru now has two handlers: the default one at the `DEBUG` level, and your new one at the `TRACE` level. To fix this, you need to remove the default handler first:

```python
>>> import sys
>>> from loguru import logger

>>> logger.remove()
>>> logger.add(sys.stderr, level="TRACE")
1

>>> logger.trace("This WILL be visible")
2025-02-03 10:25:38.508 | TRACE    | __main__:<module>:1 - This WILL be visible

>>> logger.debug("Now this appears exactly once")
2025-02-03 10:25:38.509 | DEBUG    | __main__:<module>:1 - Now this appears exactly once

>>> logger.info("As does this info message")
2025-02-03 10:25:38.669 | INFO     | __main__:<module>:1 - As does this info message
```

The `logger.remove()` method, when called without an argument, effectively removes all existing handlers, including the default one, allowing you to start with a clean slate before adding your new handler.

**Note:** To remove only the default handler, you can pass `0` to `logger.remove()`. Loguru always assigns the identifier `0` to the default handler, so `logger.remove(0)` will remove it. For any other handlers, you would pass the identifier that was returned when you added the handler. Your code will now behave as expected.

Understanding the log levels helps you implement appropriate logging strategies for different environments. For example, you might use the `DEBUG` level in development but only `WARNING` and above in production. Next, you’ll look at how to customize the format and filtering of your log messages to make them even more useful.

### Customizing Logs

While Loguru’s default configuration is useful out of the box, you’ll often want to customize how your logs look and which messages get logged. Loguru makes this customization straightforward through the `.add()` method with a `format`parameter.

Start with a format that includes just the essential log message without any additional metadata or formatting:

```python
>>> import sys
>>> from loguru import logger

>>> logger.remove()
>>> logger.add(sys.stderr, format="{message}")
1

>>> logger.info("A minimal log message")
A minimal log message
```

Here, you use the `{message}` placeholder to display just the log message with no other information.

When you run this code, you’ll see a clean, minimalist output with only the message content. There are no timestamps, log levels, or source information. This bare-bones approach might be useful for very specific use cases where you need logs to be as concise as possible:

```
A minimal log message
```

The `format` string supports several placeholders that get replaced with actual values:

- `{time}`: Timestamp
- `{level}`: Log level
- `{message}`: The actual log message
- `{name}`: Module name
- `{line}`: Line number

For better context, and better troubleshooting and log analysis, you’ll typically want to include at least a timestamp and log level in your format string:

```python
>>> import sys
>>> from loguru import logger

>>> logger.remove()
>>> logger.add(
...     sys.stderr,
...     format="{time:YYYY-MM-DD HH:mm:ss} | {level} | {message}"
... )
1

>>> logger.info("A more informative log message")
2025-02-03 10:26:27 | INFO | A more informative log message
```

This format combines the `{time}` placeholder with a custom date format, the `{level}` placeholder to indicate severity, and the `{message}` placeholder for your actual log content. The pipe characters (`|`) create visual separation between the components, making logs easier to scan when troubleshooting issues.

Notice that the `time` placeholder is further customized by passing a format string (`YYYY-MM-DD HH:mm:ss`) to show the full date and time in a readable format.

To help with debugging, you might want to include the **module name** and **line number** to see exactly where a log message originated:

```python
>>> import sys
>>> from loguru import logger

>>> logger.remove()
>>> logger.add(
...     sys.stderr,
...     format="[{time:HH:mm:ss}] >> {name}:{line} >> {level}: {message}"
... )
1

>>> logger.info("Database connection established")
[14:02:51] >> __main__:1 >> INFO: Database connection established
```

In this example, the `format` string creates a detailed log structure that’s extremely useful for debugging. Each component serves a specific purpose in helping you pinpoint exactly where and when each log was generated. Here’s a breakdown of the elements in this custom format:

- `[{time:HH:mm:ss}]`: Time in brackets, showing hours, minutes, and seconds
- `{name}`: The module name
- `{line}`: The line number where the logging call was made
- `{level}`: The severity level of the log message
- `{message}`: The actual log message
- `>>`: Custom separators between elements for better readability

For a complete list of available formatting placeholders, refer to the Loguru documentation on the [record dict attributes](https://loguru.readthedocs.io/en/stable/api/logger.html#record).

Loguru also supports **color markup** in your format strings. You can use tags like `<red>`, `<green>`, `<blue>`, and others, and they’ll automatically be converted into terminal colors:

```python
>>> import sys
>>> from loguru import logger

>>> logger.remove()
>>> logger.add(
...     sys.stderr,
...     format=(
...         "[<red>{time:HH:mm:ss}</red>] >> "
...         "<yellow>{level}</yellow>: "
...         "<cyan>{message}</cyan>"
...     )
... )
1

>>> logger.info("Database connection established")
[14:02:57] >> INFO: Database connection established
```

In this example, the `format` string uses color markup tags to highlight different parts of the log message. The timestamp appears in red, the log level in yellow, and the message is in cyan. The format string is broken into multiple lines for readability, but it’s treated as a single continuous string.

The result will be similar to the following image, although your colors may vary depending on your terminal and theme:

[![Loguru Colors](https://files.realpython.com/media/Loguru_Colors.5f5ce56de443.png)](https://files.realpython.com/media/Loguru_Colors.5f5ce56de443.png)

The colors are automatically disabled if the output doesn’t support them, such as when logging to a file.

### Adding Context to Logs

Often, you’ll want to include extra **context** in your log messages to help you better understand what was happening when the log was created. Loguru provides several ways to add this context. The simplest approach is to pass **keyword arguments**directly to your logging methods:

```python
>>> import sys
>>> from loguru import logger

>>> logger.remove()
>>> logger.add(
...     sys.stderr,
...     format="{time} | {level} | {message} | {extra}"
... )
1

>>> logger.info("User logged in", user_id=123)
2025-02-03T10:27:43.451744+0000 | INFO | User logged in | {'user_id': 123}
```

Here, you pass a keyword argument `user_id=123` to `logger.info()` to add extra context. This approach is perfect for one-off contextual information that you only need for a specific log entry. The context is automatically added to the `extra`dictionary.

For context that needs to persist across multiple log entries, Loguru provides two powerful methods:

- `.bind()`: Creates a new logger with permanent context for all logs
- `.contextualize()`: Temporarily adds context within a code block using a context manager

The `.bind()` method is useful when you want to attach persistent information to every log message from a specific logger instance, such as user IDs, session IDs, or server information. This saves you from manually including this information in each log message and ensures consistency across all logs from that particular logger:

```python
>>> import sys
>>> from loguru import logger

>>> logger.remove()
>>> logger.add(sys.stderr, format="{time} | {level} | {message} | {extra}")
1

>>> user_logger = logger.bind(user_id=123)
>>> user_logger.info("User logged in")
2025-02-03T10:28:10.288620+0000 | INFO | User logged in | {'user_id': 123}

>>> user_logger.info("User started a session")
2025-02-03T10:28:11.397293+0000 | INFO | User started a session | {'user_id': 123}
```

In this example, you first configure your logger to display any extra fields by including `{extra}` in the format string. Then you create a new logger instance called `user_logger` using `.bind()`, which attaches a `user_id` to all its messages.

Every log message from `user_logger` will automatically include this user ID in its extra fields, which helps trace all actions performed by this user. 

When you run this code, each log message includes the bound context in its output—you don’t need to specify it each time. However, this context is permanent, which isn’t always ideal. Sometimes, you only need to add context that’s relevant to a specific operation or section of code, like a request ID during an API call or a transaction ID in a database operation. That’s where `.contextualize()` comes in!

The `.contextualize()` method provides a clean way to add temporary context that automatically gets cleaned up once you’re done with that operation:

```python
>>> import sys
>>> from loguru import logger

>>> logger.remove()
>>> logger.add(sys.stderr, format="{time} | {level} | {message} | {extra}")
1

>>> with logger.contextualize(request_id="abc789"):
...     logger.info("Processing request")
...     logger.info("Request completed")
...
2025-02-03T10:28:30.942510+0000 | INFO | Processing request | {'request_id': 'abc789'}
2025-02-03T10:28:30.943087+0000 | INFO | Request completed | {'request_id': 'abc789'}

>>> logger.info("Request is processed, this will not show extra context")
2025-02-03T10:28:31.026640+0000 | INFO | Request is processed, this will not show extra context | {}
```

In this example, the `.contextualize()` method acts as a Python context manager that temporarily adds a `request_id` to the logs. Inside the `with` block, all log messages automatically include this ID, but once you exit the block, the context is removed. This is very useful when handling web requests or database transactions where you want to group all logs related to a single operation.

Unlike `.bind()`, which creates a new logger instance, `.contextualize()` modifies the global logger’s context temporarily and then restores it to its previous state.

When working with larger applications, you often need both permanent identifiers—like user IDs—and temporary ones, such as request or transaction IDs. By combining `.bind()` and `.contextualize()`, you can build a complete picture of what’s happening in your application:

```python
>>> import sys
>>> from loguru import logger

>>> logger.remove()
>>> logger.add(sys.stderr, format="{time} | {level} | {message} | {extra}")
1

>>> user_logger = logger.bind(user_id=123)
>>> with user_logger.contextualize(request_id="abc789"):
...    user_logger.info("Processing user request")
...
2025-02-03T10:28:51.430919+0000 | INFO | Processing user request | {'request_id': 'abc789', 'user_id': 123}

>>> user_logger.info("Another user action unrelated to previous request")
2025-02-03T10:28:51.556306+0000 | INFO | Another user action unrelated to previous request | {'user_id': 123}
```

This code demonstrates a permanently bound `user_id` that tracks all actions for a specific user, a temporary `request_id` for the duration of a single operation, and merged contexts in the output where both IDs appear (`{'request_id': 'abc789', 'user_id': 123}`).

This pattern is a great way to trace operations in web applications where you track both the user making requests and individual request operations.

The key difference between `.bind()` and `.contextualize()` is that `.bind()` creates a new logger instance with permanent context, while `.contextualize()`temporarily adds context within a specific block of code. You can combine them when you need both permanent and temporary context.

Now that you understand how to enrich your logs with context data, you’ll take a look at the technical challenge of **log storage** and management. This becomes important when your application runs for a long time and generates large amounts of log data. In the next section, you’ll explore how to save your logs to files and use Loguru’s powerful **rotation** and **retention** features to keep them organized and manageable.

### Logging to Files Using Loguru

While logging to the console is useful during development, in production, you’ll typically want to write logs to files. Loguru enables setting up file logging with advanced features like log rotation and retention policies.

Before you take a look at example implementations, you’ll need to understand the two main management policies that Loguru provides for neatly organizing your log files:

- **Log rotation:** Determines when to start a new log file, based on file size, time intervals, or other conditions.
- **Log retention:** Controls how long to keep old log files before deleting them, based on file count or age.

For a bare-bones example of file logging without these policies, start with the basics. First, use a simple log file:

```python
>>> from loguru import logger
>>> logger.add("app.log")
1
>>> logger.info("This message goes to app.log")
2025-02-03 10:53:29.498 | INFO     | __main__:<module>:1 - This message goes to app.log
```

Here, you call the logger’s `.add()` method to specify a log message destination, known as a **sink**. By passing the string `"app.log"` as the sink, you direct log messages to be written to this file. With this, in addition to logging to the console, a new file called `app.log` will also be created if it doesn’t exist, or be appended to if it does.

Loguru intelligently handles different sink types so that logs are sent to the correct destination based on the value you pass to `.add()`.

The `app.log` is a basic text file that you can open in any text editor:

```
app.log
2025-02-03 10:53:29.498 | INFO     | __main__:<module>:1 - This message goes to app.log
```

You’ll notice that this basic setup, while simple, has some limitations: the file will grow indefinitely as your application runs. Log rotation, which automatically creates new log files based on size or time conditions, solves this problem by preventing any single file from becoming too large.

As your application runs, you can control when new log files are created using rotation:

```python
>>> from loguru import logger

>>> logger.add("app.log", rotation="4 KB")
1

>>> for i in range(100):
...     logger.info(f"Processing item #{i}")
...
2025-02-03 10:54:34.971 | INFO     | __main__:<module>:2 - Processing item #0
2025-02-03 10:54:34.972 | INFO     | __main__:<module>:2 - Processing item #1
(...)
```

In this example, you write multiple log messages in a loop to demonstrate rotation in action. When the log file reaches four kilobytes in size, Loguru automatically renames the current log file to include a timestamp, such as `app.2025-02-03_10-54-34_188928.log`. Then, it creates a fresh `app.log` file and continues writing new messages to it.

This continues as your application runs, and a new file is created each time the size limit is reached. The latest logs are always saved to `app.log`. The same principle applies when using **time-based rotation** instead of **size-based rotation**. For example, if you set `rotation` to `"00:00"`, the current file would be renamed exactly at midnight, and a new one will be immediately started.

The `rotation` parameter defines when to start a new log file. It accepts several different types of arguments:

- **Size-based:** Creates a new file when size reaches a limit (`"100 MB"`, `"2 GB"`).
- **Time-based:** Creates a new file at specific times (`"00:00"` for daily).
- **Interval-based:** Creates a new file after set durations (`"12 hours"`, `"3 days"`).

But what happens to these rotated files? By default, they accumulate indefinitely, which consumes disk space. Loguru’s **retention** feature helps manage this by automatically cleaning up old log files:

```python
>>> import time
>>> from loguru import logger

>>> logger.add("app.log", rotation="5 seconds", retention="1 minute")
1

>>> for i in range(100):
...    logger.info(f"Processing item #{i}")
...    time.sleep(2)
...
2025-02-03 10:55:49.298 | INFO     | __main__:<module>:2 - Processing item #0
2025-02-03 10:55:51.309 | INFO     | __main__:<module>:2 - Processing item #1
(...)
```

In the `.add()` example above, you configure the logger to rotate log files every `5`seconds and keep them for only `1` minute, then simulate a logging workload. The code does a few things:

1. Sets up an `app.log` file sink with rotation every `5` seconds and retention for `1`minute. These two parameters are for demonstration only, and realistically, the durations will be way longer.
2. Inside a `for` loop, a message is logged, then the program waits for `2` seconds using the `time.sleep()` function from the `time` module.
3. Logging continues for 100 iterations, creating multiple rotated log files.
4. Old log files are deleted after they exceed the `1`-minute retention period.
5. This creates a rolling window of log files, where new logs continue to be written while expired files older than `1` minute are removed.

The `retention` parameter can be either **count-based** or **time-based**:

- **Count-based**: Keeps only the `N` most recent files. For example, `retention=3`keeps only the three newest log files and deletes older ones.
- **Time-based**: Removes files older than the specified duration. For example, `retention="1 week"` automatically deletes any log files older than one week.

Now that you have a good understanding of basic logging, formatting, contextual data, and log file management, you know how to create a great logging system. However, when things go wrong in your application, you need more than just log messages—you need detailed error information that helps you track down and fix problems quickly. In the next section, you’ll look at how to enhance your error handling with Loguru’s powerful debugging features.

## Error Handling and Debugging With Loguru

When you’re debugging applications, just knowing an error occurred from log messages isn’t enough. You need to understand exactly what happened and why. Loguru provides powerful error-handling features that give you detailed error information, complete with variable values and execution context. In this section, you’ll learn how to capture and debug errors effectively using Loguru’s built-in features.

### Using the `@logger.catch` Decorator

The simplest way to catch and log errors is with Loguru’s `@logger.catch` decorator. It’s especially useful when you want to ensure that any errors in your application are automatically logged as they occur:

```python
catch_and_log_errors.py
from loguru import logger

@logger.catch
def divide(a, b):
    return a / b

divide(10, 0)
```

In this example, you use the `@logger.catch` decorator to automatically catch and log any exceptions that occur in `divide()`. As you can see, there’s a division by zero in the `divide(10, 0)` call, which will raise a `ZeroDivisionError` exception.

To see how this works, you can execute this script in your terminal:

```bash
(venv) $ python catch_and_log_errors.py
```

Loguru provides more detailed information in its log to ensure thorough traceability for debugging with a full traceback, including variable values at the moment the error occurred:

```
2025-02-03 13:48:38.008 | ERROR    | __main__:<module>:7 - An error has been caught in function '<module>', process 'MainProcess' (9562), thread 'MainThread' (127338692705152):
Traceback (most recent call last):

> File "/home/user/catch_and_log_errors.py", line 7, in <module>
    divide(10, 0)
    └ <function divide at 0x73d058a1cd60>

  File "/home/user/catch_and_log_errors.py", line 5, in divide
    return a / b
           │   └ 0
           └ 10

ZeroDivisionError: division by zero
```

In this output, the `@logger.catch` decorator automatically captures the `ZeroDivisionError` error, logs it with a traceback, and provides useful context. This includes the timestamp of the error, process and thread information, a traceback showing exactly where the error happened—along with variable values—and the actual error message and type.

This automatic error catching is particularly useful during development as it ensures no exceptions go unnoticed. The decorator can be applied to any function where you want comprehensive error logging.

### Using Improved Tracebacks With Customized Error Messages

Using the basic `@logger.catch` decorator is a good idea because it requires zero configuration and catches all exceptions automatically. However, in larger applications, you often need more control over how errors are logged and handled. You might want to customize the error message, set specific log levels, or handle certain types of errors differently. To solve this, you can customize the `@logger.catch` decorator with additional parameters:

```python
customized_log_errors.py
from loguru import logger

@logger.catch(message="Database connection failed", level="ERROR")
def connect_to_db(host, port):
    if port < 1000:
        raise ValueError("Invalid port number")
    # Simulated database connection
    return 1 / 0  # Simulate error

connect_to_db("localhost", 123)
```

In this example, you’re simulating a database connection. The `@logger.catch`decorator takes two important arguments: a custom `message` that provides context about what failed, and a `level` parameter that ensures the error is logged with the appropriate severity.

The `connect_to_db()` function has two parameters: the `host` address and `port`number. It includes a basic validation check that raises a `ValueError` if someone tries to use a port number below `1000` . After this check, it simulates a database connection failure using a division by zero operation.

Run the script in your terminal to see it in action:

```bash
(venv) $ python customized_log_errors.py
```

You’ll get output that shows the log message, the exception that was raised, and the full context of the operation:

```
2025-02-03 13:50:53.549 | ERROR    | __main__:<module>:10 - Database connection failed
Traceback (most recent call last):

> File "/home/user/customized_log_errors.py", line 10, in <module>
    connect_to_db("localhost", 123)
    └ <function connect_to_db at 0x7c509a377a60>

  File "/home/user/customized_log_errors.py", line 6, in connect_to_db
    raise ValueError("Invalid port number")

ValueError: Invalid port number
```

Here, the customized log message appears on the first line. During the `connect_to_db("localhost", 123)` call, the port number `123` fails the validation check, raising `ValueError`. The `@logger.catch` decorator intercepts this error, wraps it with your custom message, and logs it at the `ERROR` level, all while preserving the full context of what went wrong.

This pattern is really useful in production applications where you need to quickly identify and diagnose issues. Instead of generic error messages, you get contextual information that helps you understand exactly what part of your application failed and why. For even more context, you can use the contextualization helpers you explored earlier. You’ll take a closer look at it in the next section.

### Debugging With Contextualized Errors

In certain scenarios, you’ll likely need to understand not just where an error occurred, but also the state of your application when it happened. To achieve this, you can combine error handling and contextual information with the help of the `.contextualize()` context manager you saw earlier. Here’s an example of how you can use it to create highly informative debug logs:

```python
debug_with_context.py
import sys
from loguru import logger

logger.remove()
logger.add(
    sys.stderr,
    format="{time} | {level} | {message} | {extra}",
    level="TRACE"
)

@logger.catch
def perform_action(user, action):
    with logger.contextualize(user=user, action=action):
        logger.trace("Starting action")
        logger.info("Performing action")
        if action not in ["login", "logout"]:
            logger.trace("Invalid action detected")
            raise ValueError("Invalid action")
        logger.success("Action completed")

perform_action("alice", "delete")
```

In the example above, you have a combination of contextualized logging and error handling that provides a complete picture of what happened before, during, and after an error occurs. Here’s a breakdown of how it works:

- **Lines 1 and 2**: Import the `sys` module and the `logger` object.
- **Lines 4 to 9**: Remove the default handler and add a custom one that shows context information (`{extra}`) and sets the minimum level to `"TRACE"` to capture all log messages.
- **Lines 11 to 19**: Define the `perform_action()` function that takes a `user` and `action` parameters, then uses contextualization to attach this information to all logs within its scope.
- **Line 21**: Calls the function with parameters that trigger the error condition.

Now run this `debug_with_context.py` script in your terminal to see all the logs and traceback that will be produced:

```bash
(venv) $ python debug_with_context.py
```

You’ll see a full sequence of log levels that tells a story:

```
2025-02-04T11:26:24.042435+0100 | TRACE | Starting action | {'user': 'alice', 'action': 'delete'}
2025-02-04T11:26:24.042639+0100 | INFO | Performing action | {'user': 'alice', 'action': 'delete'}
2025-02-04T11:26:24.042761+0100 | TRACE | Invalid action detected | {'user': 'alice', 'action': 'delete'}
2025-02-04T11:26:24.042891+0100 | ERROR | An error has been caught in function '<module>', process 'MainProcess' (8238), thread 'MainThread' (128347999026048): | {}
Traceback (most recent call last):

> File "/home/user/debug_with_context.py", line 21, in <module>
    perform_action("alice", "delete")
    └ <function perform_action at 0x74bb575837e0>

  File "/home/user/debug_with_context.py", line 18, in perform_action
    raise ValueError("Invalid action")

ValueError: Invalid action
```

In this sequence, `.trace()` logs the very beginning of the operation, then `.info()`indicates the main action being attempted. Another `.trace()` captures the moment right before the error. Finally, an error is caught by `@logger.catch` to indicate an invalid action.

When you call `perform_action("alice", "delete")`, the function fails because `"delete"` isn’t an allowed action. However, because of the tracing and context, you get a complete log of what happened:

- **Who** tried to perform the action: `alice`
- **What** they tried to do: `delete`
- **Each step** that executed before the failure
- **The exact point** where the validation failed

This combination of contextual logging and error handling is great for debugging complex applications where you need to understand the sequence of events that led to an error.

**Note:** Now you have solid understanding of how to handle errors and troubleshoot your Python programs! For even more advanced debugging capabilities, you may want to explore interactive debugging tools like Python’s built-in debugger, `pdb`.

There’s one last thing to consider. As your application grows and your logging needs become more complex, it’s important to make your logs more accessible to automated analysis tools and logging aggregators. Structured logging formats like JSON can help you achieve this goal. In the next section, you’ll look at how to format your logs as structured data that’s both machine-readable and human-friendly.

## Structured Logging for Modern Applications

Structured logging helps you organize your log data in a consistent, machine-readable format while keeping it human-readable. Loguru provides two main approaches to structured logging:

1. Serializing your entire log output as JSON
2. Selectively adding structured data using extra fields

In this section, you’ll learn how to use both methods to create well-organized logs that work perfectly with logging tools and aggregators.

### Formatting Logs as JSON

When you need your logs in a structured format, Loguru allows you to output them as JSON. This is particularly useful when you’re working with log analysis tools, or when you need to process your logs programmatically.

To get a first impression of how JSON formatting works in logs, add a `serialize`parameter to the `.add()` method:

```python
loguru_json_format.py
import sys
from loguru import logger

logger.remove()
logger.add(
    sys.stderr,
    serialize=True
)

logger.info("User logged in", user_id=123)
```

Here, you first remove the default handler and add one with JSON serialization by passing `serialize=True` to the `.add()` method. The `serialize` parameter tells Loguru to convert each log message into a structured JSON object instead of the default text format. Notice that you can still pass additional contextual data, like `user_id`, as keyword arguments to the logging call, and Loguru will automatically include them in the JSON output’s `extra` field.

You can now run this script to generate a JSON object for each log message:

```bash
(venv) $ python loguru_json_format.py
```

Instead of Loguru’s default formatted output that you’ve seen before, you’ll get a JSON object for each log message:

```json
{
  "text": "2025-02-04 15:19:00.504 | INFO | __main__:<module>:1 - User logged in\n",
  "record": {
    "elapsed": {
      "repr": "0:00:00.022562",
      "seconds": 0.022562
    },
    "exception": null,
    "extra": {
      "user_id": 123
    },
    "file": {
      "name": "<stdin>",
      "path": "<stdin>"
    },
    "function": "<module>",
    "level": {
      "icon": "ℹ️",
      "name": "INFO",
      "no": 20
    },
    "line": 1,
    "message": "User logged in",
    "module": "<stdin>",
    "name": "__main__",
    "process": {
      "id": 13745,
      "name": "MainProcess"
    },
    "thread": {
      "id": 140715465190272,
      "name": "MainThread"
    },
    "time": {
      "repr": "2025-02-04 15:19:00.504204+01:00",
      "timestamp": 1738678740.504204
    }
  }
}
```

The JSON output includes all the information about your log message in a structured format, with the original formatted text in the `text` field and the structured data in the `record` field. Any contextual data passed as keyword arguments to the logger, such as `user_id=123`, appears in the `extra` field of the record.

Of course, you can use a file as an output for your logs, just like you did previously. This time, the log file will be filled with one JSON object per line, so you can process it with standard JSON parsing tools, such as `jq`:

```python
loguru_json_file.py
from loguru import logger

logger.remove()
logger.add(
    "app.json",
    serialize=True,
    format="{time} | {level} | {message}"
)

logger.info("Application started")
logger.warning("Memory usage high")
```

Here, you configure Loguru to write serialized logs to a file named `app.json` instead of `stderr`, just as you did in the **Logging to Files Using Loguru** section. The `serialize=True` parameter sets up JSON formatting, while the `format` parameter defines how the text portion of each log entry will appear.

When you run this script, a new `app.json` will be created if it doesn’t exist, or be appended to if it does:

```bash
(venv) $ python loguru_json_file.py
```

Each logging call will create a new JSON object in the file in each new line. This new `app.json` file will have all the log data you’d expect, and will be structured and optimized for log analysis tools and APIs:

```json
app.json
{"text": "2025-02-04T15:28:36.583972+0100 | INFO | Application started\n", "record": {"elapsed": {"repr": "0:00:00.027052", "seconds": 0.027052}, "exception": null, "extra": {}, "file": {"name": "<stdin>", "path": "<stdin>"}, "function": "<module>", "level": {"icon": "ℹ️", "name": "INFO", "no": 20}, "line": 1, "message": "Application started", "module": "<stdin>", "name": "__main__", "process": {"id": 14132, "name": "MainProcess"}, "thread": {"id": 129594171026304, "name": "MainThread"}, "time": {"repr": "2025-02-04 15:28:36.583972+01:00", "timestamp": 1738679316.583972}}}
{"text": "2025-02-04T15:28:37.063342+0100 | WARNING | Memory usage high\n", "record": {"elapsed": {"repr": "0:00:00.506422", "seconds": 0.506422}, "exception": null, "extra": {}, "file": {"name": "<stdin>", "path": "<stdin>"}, "function": "<module>", "level": {"icon": "⚠️", "name": "WARNING", "no": 30}, "line": 1, "message": "Memory usage high", "module": "<stdin>", "name": "__main__", "process": {"id": 14132, "name": "MainProcess"}, "thread": {"id": 129594171026304, "name": "MainThread"}, "time": {"repr": "2025-02-04 15:28:37.063342+01:00", "timestamp": 1738679317.063342}}}
```

In the `app.json` file, each line is a complete JSON object, one for each log message. Each object contains the formatted message text and a detailed record of information about when and where the log was created. The two log entries maintain their different severity levels—`INFO` and `WARNING`—and include identical structure but different content and timestamps.

**Note:** The resulting `app.json` file isn’t a single JSON document but rather a series of JSON objects, one per line. This format is known as JSON Lines (or JSONL). It’s commonly used for log files because it allows you to append new log entries and helps with stream processing of the file.

This standard format works well for many logging scenarios, but it can be quite verbose when you’re working with high-volume applications. For large-scale systems, you might need a more concise format that focuses only on the most relevant information. Fortunately, Loguru gives you the flexibility to customize your JSON output with exactly the fields you need.

### Creating Custom JSON Formats

Loguru’s built-in JSON serialization is powerful, but it produces verbose output with more information than you might need. When working with log analysis tools like [Logstash](https://www.elastic.co/logstash) or [Grafana Loki](https://grafana.com/docs/loki/latest/), you’ll often want concise, focused log entries that contain only essential data.

The good news is that Loguru allows you to create custom serialization functions, which will simplify the default JSON structure, and you can precisely control which fields appear in your logs. This leads to more readable output, optimized storage usage, and better integration with external analysis tools:

```python
custom_json_logging.py
import json
from loguru import logger

def simple_serializer(record):
    subset = {
        "time": record["time"].timestamp(),
        "level": record["level"].name,
        "message": record["message"],
        "context": record["extra"]  # Include any bound context
    }
    return json.dumps(subset)

def add_serialization(record):
    record["extra"]["json_output"] = simple_serializer(record)

logger.remove()
logger = logger.patch(add_serialization)
logger.add("custom.json", format="{extra[json_output]}")
logger.bind(user="john").info("User logged in")
logger.bind(order_id=12345).info("Order processed")
```

This example solves the verbosity problem by creating a custom serialization function that includes only the fields you actually need. Here’s how it works:

- **Lines 1 and 2**: Import the `json` module and the `logger` object.
- **Lines 4 to 11**: Define the `simple_serializer()` function that extracts only four essential fields from the log record: timestamp, level name, message text, and contextual data from `extra`. It then converts this subset to a compact JSON string using `json.dumps()`.
- **Lines 13 and 14**: Create the `add_serialization()` function that will be applied to each log record. This function calls `simple_serializer()` and stores the resulting JSON in the record’s `extra` dictionary under the `"json_output"` key.
- **Line 16**: Removes all default handlers with `logger.remove()` to start with a clean configuration.
- **Line 17**: Patches the `logger` with the `add_serialization()` function using `.patch()`, which ensures every future log record is processed by this function.
- **Line 18**: Adds a file handler that writes to `custom.json` and uses `format="{extra[json_output]}"` to output only the customized JSON.
- **Lines 19 and 20**: Demonstrate logging with different contextual data using `.bind()`. Each log call produces a compact, focused JSON entry with the user’s context included.

You can run this script to see it in action. A new `custom.json` file will be created and contain the compact JSON you’ve defined, in addition to the right context: 

```bash
(venv) $ python custom_json_logging.py
```

When you run this code, the produced `custom.json` file will contain clean, concise JSON entries that focus only on the essential information you need for log analysis:

```json
custom.json
{
  "time": 1740220156.181082,
  "level": "INFO",
  "message": "User logged in",
  "context": {
    "user": "john"
  }
}
{
  "time": 1740220156.181273,
  "level": "INFO",
  "message": "Order processed",
  "context": {
    "order_id": 12345
  }
}
```

Each line contains a complete JSON object with timestamp, log level, message text, and contextual data in a format that’s both human-readable and easily programmatically parsable by machines.

Using `.patch()` instead of the `serialize` parameter has several advantages:

1. It gives you more control over how records are processed.
2. It lets you modify records in place before they reach any handlers.
3. It allows for more complex transformations beyond basic serialization.
4. It works consistently across multiple handlers.

This makes `.patch()` a handy way to customize log serialization in production applications where you need fine-grained control over your log output format.

**Note:** The `format="{extra[json_output]}"` directive is very important because it tells Loguru to use only your custom-serialized JSON string as the output, rather than applying any additional formatting. Without this specific format string, Loguru would apply its default formatting to your already-serialized JSON, potentially causing format issues or duplicate content.

This approach gives you complete control over your log format while maintaining the convenience of structured logging. You can, of course, customize the `simple_serializer()` function to include any additional fields that are important for your specific application or logging requirements.
