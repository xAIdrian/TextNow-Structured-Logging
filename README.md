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

## The Current State of Logging
