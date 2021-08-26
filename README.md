# TextNow-Structured-Logging

## The Mission

Our mission is to give our developers a comprehensive strategy and tooling to quickly and easily access uploaded logs in a human-readable structure for debugging purposes, additionally enabling access to these logs by data teams in a machine readable format.

## The Problem

Logging in our native Android application is virtually non-existent at this point with each developer implementing their own strategy to debug logging with no communication across teams.  Leading our logging strategy to atrophy with existing resources going unused.  

The introduction of new SaaS platform to our support application has provided teams with a large amount of information in regards to the various contexts of our user's experience.  The success of these integrations has empowered the Android team and improved our overall effectiveness.

However, this success has also produced a number of challenges. These include:

**1. GUI Focused Tools**

The current tools we have at our disposal giving us insights into the user's experience and technical issues are accessed through online dashboards, consumed in a limited number of ways.  We do not own the data so we are limited in the flexibility of approaches we can take to gain additional insights from the same data.

**2. Combursome Consumption of Debug Logs**

At this stage, developer logs are not accessible to the majority of our developers on Android.  The amount of effort to get these logs onto your local machine from our S3 buckets is only a part of the problem.  The information is not structured in any meaningful way and is uploaded to our Amazon servers only when their is an application crash or the user explicitly opts-in to send us their logs.  Meaning their may be a period of days between log uploads.  Since they are difficult to read and do not provide much valuable information they are often unused.  

**3. Varied Approaches to Logging**

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

With the local implementation to take these logs off of the main thread, put them in file storage, and eventually upload them are encapsulated within the current `textnow-android` module.  Several concerns exist with the existing solution and have been documented :

- One upload request available/alive at a time
- ExistingWorkPolicy.KEEP (LogUploadWorker) will drop incoming log uploads if previous uploads fails or is in progress.  Any newly appended log upload task will never execute since the unique chain has failed.

![image](https://user-images.githubusercontent.com/7444521/130965886-61357144-2a1f-4638-90b7-2a7814e73890.png)


## The Solution
### Overview

### Objectives
