---
layout: post
title: "Creating Formatted Log Messages Using Variadic Arguments in C"
category: "Black Magic"
author: silas-bartha
---

Oftentimes when creating a logging system for a project I'm working on, I like to write the interface for the logger to be as easy-to-use as possible. One of the best ways to do this in my opinion is allowing the user to use string formatting in your logger functions.

In C# this is relatively simple to do. The string formatting method has the following declaration:

```cs
    String Format(String format, params object?[] args)
```

To allow for formating in your logger function, you could simply write another method like the following:

```cs
    public void Log(int level, String format, params object?[] args) {
        // Create some things that your log messages will all have in common, I just used a timestamp for example...
        String formattedMsg = String.Format(format, args);
        String messageToLog = String.Format("[%s] %d - %s", timestamp, level, formattedMsg);
        // ...Write message to log file or anywhere else
    }
```

Now in C this is a little more difficult due to the way variadic arguments are handled.

```c
    int sprintf(char* str, const char* format, ...);
```

The argument **...** allows for multiple arguments to be passed to the function, but this keyword has no identifier or type to refer to when passing it through.

```c
    void logMessage(int level, char* fmt, ...) {
        // Create some things that your log messages will all have in common, I just used a timestamp for example...
        char formattedMsg[SOME_MESSAGE_LENGTH_CONSTANT] = {0};
        sprintf(formattedMsg, fmt, /*???*/);

        // ...Write message to log file or anywhere else
}
```

So we can't actually pass variadic arguments through to **sprintf**. Don't worry, they thought of us and there's still a way to do what we want.

Introducing...
```c
    int vsprintf(char* str, const char* format, va_list ap);
```

The **vsprintf** function allows us to pass an argument of the **va_list** type, which is a special type that represents a list of arguments. Now, we can rewrite our function to create an instance of this type.

```c
    void logMessage(int level, char* fmt, ...) {
        // Create some things that your log messages will all have in common, I just used a timestamp for example...
        char formattedMsg[SOME_MESSAGE_LENGTH_CONSTANT] = {0};
    
        va_list args = {0};
        va_start(args, fmt);
        vsprintf(formattedMsg, fmt, args);
        va_end(args);

        fprintf(SOME_FILE, "[%s] %d - %s", timestamp, level, formattedMsg);
    }
```

In this example, we create a **va_list** instance called "args" and then call the **va_start** function to populate it with every argument passed to our function after the **fmt** argument. This gives us all arguments represented by the **...** in our parameter list. After we are finished with the argument list instance we must call **va_end** to clean up. Once we have our **va_list**, we can then pass it to the **vsprintf** function to format our log message.

It's not too difficult to do, and the use of variadic argument lists in this way saves a lot of time when writing log messages in C.

Something to note: You should *never* allow for user-input format strings. Ever. This makes your program [vulnerable](https://en.wikipedia.org/wiki/Uncontrolled_format_string).
