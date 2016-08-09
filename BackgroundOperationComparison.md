**Purpose of this document:**

This document is intended as a high level survey of what background capabilities are allowed by operating systems and browsers.

**Operating Systems**

All of the major desktop operating systems allow native applications to do significant background work, limited only by scheduling algorithms that prevent starvation of other processes.

Android has historically had a very open policy for background work, allowing any installed app to do background processing and use network resources. However, that caused extreme battery life and network bandwidth problems for users. In recent releases the OS has started being more restrictive of background operations. In Android Marshmallow, Doze mode was introduced, during which time background processing was limited, and in Nougat, Doze mode has been expanded to be much more aggressive. Additionally, Android is moving to a more controlled model with expansions to the Job Scheduler in Nougat, which gives apps a more restricted set of background operation triggers.

While Android was historically permissive and is now becoming more restrictive, iOS has followed the opposite path. iOS has always limited the types of apps that can do background processing, although it has expanded those types over time using the Background Execution framework. Particularly interesting is that in iOS7, the ability to respond to push messages and run for 30 seconds was added. Since then, more types of apps have been allowed to run background tasks, and more system services have been developed to allow apps to hand off tasks such as large downloads to the system.

**References**

 - [Android Doze Mode](https://developer.android.com/training/monitoring-device-state/doze-standby.html)
 - [Android Job Scheduler](https://developer.android.com/preview/features/background-optimization.html)
 - [iOS Background Execution](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)

**Browsers**

There are two paths to background operations in browsers: browser extensions and service workers. The two big differences between these are that extensions require a user to explicitly install them, and they are proportionately more powerful. Extensions can run any time the browser is running, and can affect all origins accessed by the browser. Service Workers, in contrast, have limits on how long they can run without a foregrounded page and can only affect their installed origin.

All major browsers support some form of extensions, and all of them allow unlimited background processing within the browser. Firefox, Chromium, and Opera support very similar mechanisms of background operation called Background Pages. These allow for script execution within the browser at any time. Safari also allows background operation for its extensions, and while the Edge extension framework is still in development, it will likely allow similar abilities for installed extensions.

Both Chromium based browsers and Firefox support service workers, but they have different levels of allowed background work. Chromium allows service worker processing either in response to a push message or a background sync operation. In the case of the push message, the service worker is required to show a notification if it isn’t in the foreground. In the case of background sync, operation is time limited to 5 minutes. 
Firefox allows background service worker activity when triggered by a push message, but limits how often that happens based on a hidden engagement heuristic. Once the service worker has exhausted its internal quota, no future push messages are allowed.

Edge has publicly stated that service workers will be supported in the browser in the future, but has not released details of their implementation. Safari has released no information. 

**References**

 - [Mozilla Add-On Background Pages](https://developer.mozilla.org/en-US/Add-Ons/WebExtensions/Anatomy_of_a_WebExtension#Background_scripts)
 - [Chromium Extension Background Pages](https://developer.chrome.com/extensions/background_pages)
 - [Opera Background Process](https://dev.opera.com/extensions/architecture-overview/)
 - [Safari Extension Background operation](https://developer.apple.com/library/safari/documentation/Tools/Conceptual/SafariExtensionGuide/ExtensionsOverview/ExtensionsOverview.html#//apple_ref/doc/uid/TP40009977-CH15-SW1)
 - [Android Background Sync](https://developers.google.com/web/updates/2015/12/background-sync?hl=en)
 - [Mozilla Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers)