# TextNow-Structured-Logging

> The morale effects are startling. Enthusiasm jumps when there is a running system, even a simple one. Efforts redouble when the first picture from a new graphics software system appears on the screen, even if it is only a rectangle. One always has, at every stage in the process, a working system. I find that teams can grow much more complex entities in four months than they can build.

> —FREDERICK P. BROOKS, JR., The Mythical Man-Month

## The Mission

Our mission is to give our developers a comprehensive strategy and tooling to quickly and easily access uploaded logs in a human-readable structure for debugging purposes, additionally enabling access to these logs by data teams in a machine readable format.

This can be done through the focus of three lenses :
1. Troubleshoot Application Problems
2. Identify Trends
3. Categorize Issues

## The Problem

Logging in our native Android application is virtually non-existent at this point with each developer implementing their own strategy to debug logging with no communication across teams.  Leading our logging strategy to atrophy with existing resources going unused.  

The introduction of new SaaS platform to our support application has provided teams with a large amount of information in regards to the various contexts of our user's experience.  The success of these integrations has empowered the Android team and improved our overall effectiveness.

However, this success has also produced a number of challenges. These include:

### 1. GUI Focused Tools

The current tools we have at our disposal giving us insights into the user's experience and technical issues are accessed through online dashboards, consumed in a limited number of ways.  We do not own the data so we are limited in the flexibility of approaches we can take to gain additional insights from the same data.

### 2. Combursome Consumption of Debug Logs

At this stage, developer logs are not accessible to the majority of our developers on Android.  The amount of effort to get these logs onto your local machine from our S3 buckets is only a part of the problem.  The information is not structured in any meaningful way and is uploaded to our Amazon servers only when their is an application crash or the user explicitly opts-in to send us their logs.  Meaning their may be a period of days between log uploads.  Since they are difficult to read and do not provide much valuable information they are often unused.  

### 3. Varied Approaches to Logging

Most developers on our team don't even know we are logging debug sessions on the backend outside of our various tooling solutions so it is left to each individual developer to implement logging in their own way. Usually taking the form of temporary debug logs, removed with code being merged in, and
persistent debug log that are used for QA testing.  These are communicated in Pull Requests. Often, these local logs are not cleaned up leading to logs that are meaningless for a majority of the team and adding to code complexity.

This White Paper set out to explain not only the problem faced by our current approach to debug logging but also to present an improved solution aligned with the goals of the organization.

## The Current State of Backend Hosted Developer Logs

As mentioned previously, advanced debug logging giving us deeper insights into user behavior around critical events has not been maintained over the years and is quite difficult to access.  Someone who has not been inststructed on how the current system works has a difficult understanding its value, choosing rather to leave it alone and pursue their own approach. Confluence documentation is several years old at best.

The following cases outline when a client will upload a log set to S3 through [our logging module](https://github.com/Enflick/textnow-android-logging) when :

- The client experiences a fatal crash, logs are uploaded on the next instance of app launch
- The client experiences an ANR or non fatal incident logs are uploaded
- The user manually uploads logs from the about section of the app
- A TextNow employee begins a diagnostics session

These logs are often very difficult to infer anything meaninful.
```
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] Uncaught exception on thread main (2)
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] com.enflick.android.TextNow.bubbles.BubbleChatActivity cannot be cast to com.enflick.android.TextNow.activities.MainActivity
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] MessagesRecyclerView.java:7 - com.enflick.android.TextNow.views.MessagesRecyclerView:performContextAction
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] ContextActionBarHelper.java:1 - com.enflick.android.TextNow.activities.ContextActionBarHelper$SelectionModeCallback:onActionItemClicked
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] DecorView.java:2660 - com.android.internal.policy.DecorView$ActionModeCallback2Wrapper:onActionItemClicked
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] SupportActionModeWrapper.java:1 - l.b.p.e$a:c
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] AppCompatDelegateImpl.java:1 - androidx.appcompat.app.AppCompatDelegateImpl$d:c
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] StandaloneActionMode.java:1 - l.b.p.d:a
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] MenuBuilder.java:1 - l.b.p.i.g:e
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] MenuBuilder.java:4 - l.b.p.i.g:s
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] ActionMenuView.java:2 - androidx.appcompat.widget.ActionMenuView:d
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] ActionMenuItemView.java:2 - androidx.appcompat.view.menu.ActionMenuItemView:e
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] null:3 - androidx.appcompat.view.menu.ActionMenuItemView:onClick
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] View.java:7448 - android.view.View:performClick
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] View.java:7425 - android.view.View:performClickInternal
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] View.java:810 - android.view.View:access$3600
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] View.java:28305 - android.view.View$PerformClick:run
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] Handler.java:938 - android.os.Handler:handleCallback
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] Handler.java:99 - android.os.Handler:dispatchMessage
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] Looper.java:223 - android.os.Looper:loop
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] ActivityThread.java:7660 - android.app.ActivityThread:main
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] Method.java:-2 - java.lang.reflect.Method:invoke
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] RuntimeInit.java:592 - com.android.internal.os.RuntimeInit$MethodAndArgsCaller:run
2021-08-02 12:18:43    ERROR  TextNow                        [UncaughtExceptionHandler] ZygoteInit.java:947 - com.android.internal.os.ZygoteInit:main
2021-08-02 12:18:43    DEBUG  TextNow                        [UncaughtExceptionHandler] uncaughtException: stopping call service due to uncaught exception
```

This brings with it the question of what are the links to each of these instances? When a crash happens we upload the details to s3. At the same time, we have the set of logs and keys to aws that are uploaded to the crash instance. The same happens with ANR's. When a diagnostics session is run the logs are linked to the users instance in https://manage.textnow.me though no one uses this. When users upload logs manually, there is no reference to an owner other than the fact the logs go into the s3 bucket.

With the local implementation to take these logs off of the main thread, put them in file storage, and eventually upload them are encapsulated within the current `textnow-android` module.  Several concerns exist with the existing solution and have been documented :

- One upload request available/alive at a time
- ExistingWorkPolicy.KEEP (LogUploadWorker) will drop incoming log uploads if previous uploads fails or is in progress.  Any newly appended log upload task will never execute since the unique chain has failed.

![image](https://user-images.githubusercontent.com/7444521/130965886-61357144-2a1f-4638-90b7-2a7814e73890.png)


## The Solution

Basically, we are going to build up our existing logging module for Android.  Providing more contextual, structured information to the differing log levels.  Using Android's most recent libraries to manage the local storage and asynchronous uploading of the logs. Determined by updated conditions to perform our uploads and manage the cache.

### Overview

In practice, most developers now implement structured logging to help application users interact with their log files through automated processes. Structured logging takes plain text application logs and converts them into a set of data points that can be more easily analyzed by a machine on the backend. Addresses three key issues that arise when dealing with log files in the standard "plain text" format :

1. Log files presented in plain text are formatted arbitrarily.
2. Unstructured log files are not always human-friendly. 
3. If the format changes somehow, downstream applications that depend on that specific formatting may have their function impacted.

Consider one object for each logged event. Logs will always include a timestamp and typically also include information about the state of the system, application, and various functions determined by the developer. JSON is by far the de facto standard because it is so widespread. Unlike the data shared in text-based logs, the data from structured logs are tagged in their entirety and therefore easily discernible for a machine. While nearly every structured logging design includes a human-readable field to help with troubleshooting, humans are no longer the primary audience for logs with this methodology. Instead, we intend for machines to use the logs, and then our data teams are the audience for the resulting output.

The app user's debug logs are stored in the device in the form of a database or a file. All the operations are performed in the device’s native layer. The stored logs are processed and split into chunks of predetermined size. The chunks of logs are sent to the server periodically. The chunks are deleted from the device once they are successfully transmitted to the server.

We can create the optimal solution by taking a hybrid approach and incorporating both volume based and selective logging.


### Implementation Objectives

Volume Logging – Input focused: Log all the sources, and let the analysts sort it out.

Logger sends data to a file, which we can then archive, parse, or otherwise use to match the needs that we have for that data.  The files will be managed with an LRU cache wrapping Android's file management system where we will be storing the logs. With set conditions, likely app start, the cache conditions will be checked asynchrnonously and any "stale" logs removed.

The logging is for debugging purposes. Piecing together critical moments, without an explicit user upload. Conditions will be set to upload the existing logs on the device with each app start, eventually with an arbitrary time set.  The period between logs or app sessions will not be too greate with a 20 minutes backoff for failed upload retry, which exists in our current solution.

For the initial implementation we will be using popular logging libraries, Logback natively implementing the SLF4J API to generate locally structured logs.  These logs will then be serialized and uploaded to Embrace. Where we will have the ability to filter and search for specific elements. Eventuall this will encourage monitoring and learning, parcelable and fed to data science and machine learning engines.

Will be kept performant while being able to handle a high number of requests in parallel. Users can upload logs directly through the wireless network after being parsed on the client. Only necessary information is recorded to reduce the log size.

The security of the uploaded logs will be ensured with Embrace and locally with Android's encrypted scoped storage APIs.

### Logging Strategy Objectives

Selective Logging – Output focused: Pick and choose what logs you want, and how you want them, and hope you didn’t forget anything.

The greater Android team needs to understand and agree on what events are significant enough to log and in which modules.  This can be categorized into our log levels ERROR, WARN, INFO, DEBUG, & VERBOSE.

| Log Level | Description |
| ----------- | ----------- |
| ERROR | Logs that highlight when the current flow of execution is stopped due to a failure. These should indicate a failure in the current activity, not an application-wide failure. |
| WARN | Logs that highlight an abnormal or unexpected event in the application flow, but do not otherwise cause the application execution to stop. |
| INFO | Logs that track the general flow of the application. These logs should have long-term value. |
| DEBUG | Logs that are used for interactive investigation during development. These logs should primarily contain information useful for debugging and have no long-term value. |
| VERGBOSE | Logs that track the general flow of the application. These logs should have long-term value. |

Conditions within our app flow that could result in an exception being thrown, following the "fail fast, fail early" mentality, will need to be logged through our new and improved logging module so the exception can be added to the log and uploaded.

We should NEVER log PII.  PII you should avoid logging includes, but is not limited to:

- First Name
- Last Name
- Username
- Gender
- Birthday
- Mailing/Billing Addresses
- Email Addresses
- Phone Numbers
- Social Security Number
- Credit Card Numbers

We should avoid logging secrets. Secrets you should avoid logging include, but are not limited to:

- Usernames
- Passwords
- API Keys
- Encryption Keys
- Connection Strings
- Magic URLs

## The Technology
### Proof of Concept

We'll be using [logback](http://logback.qos.ch/index.html) as our structured logging library to format our existing logs into structure, using a factory pattern. The resulting product, i.e. logback, is faster and has a smaller footprint than all existing logging systems, sometimes by a wide margin. Just as importantly, logback offers unique and rather useful features missing in other logging systems.


## Long-Term Goals

Layer of abstract analytics, like a routing layer, that consumes logs and sends them to backend analytics providers.  Serializes log events into analytics events.  This will get us away from all these different tools.

Porting logging to a different destination.  Even to the point where we integrate alerting and wake someone up.

Uploaded logs are compressed, and the log file is divided; for initial upload log files, only a simple packaged compression is conducted. However, occasionally, the size of compressed files is larger than 50 MB, which means the upload will sometimes fail. The program will subsequently and repeatedly attempt to upload logs, which uses up the user’s network bandwidth. The success rate of a large file upload is lower than that of small file uploads; thus, we created the split-and-upload file mechanism, such that problems regarding uploading logs loss and failure rarely appeared

Consider alternative entry points to upload debug logs

Improve customer confidence by showing a better message when uploading logs, to assure them someone is going to look at them. Drop down providing scope of debug log issue: 

- Calling → further breakdown → one way audio, dropped etc.
- messaging
- internet connectivity wireless service
- billing/premium
- spam/abuse/fraud
- ads/inappropreate ads
- other
