# Web Budget API
[Draft specification](https://beverloo.github.io/budget-api/)

Web Applications have conventionally been able to execute code, make network requests and interact
with the user by means of established interaction, usually through a browser tab. This has allowed
users to associate the presence of a browser tab with the Web Application's ability to do work on
their behalf.

Following the introduction of the [Push API](https://w3c.github.io/push-api/) and
[Background Synchronization](https://wicg.github.io/BackgroundSync/spec/), this assumption no longer
stands. Web Applications are now able to both trigger and schedule execution of code in the
background, outside of the user’s control. This brings Web Application
functionality closer to the scope of native applications or browser extensions,
both of which are able to perform extensive
[Background Operations](https://github.com/beverloo/budget-api/blob/gh-pages/BackgroundOperationComparison.md).

In order to protect the user, Chrome has historically required developers to
[display a notification](https://notifications.spec.whatwg.org/) in response a message. Firefox
grants developers a [budget](https://docs.google.com/document/d/1yYUB4nn9Hu6vPHKp_eN_kXOG47gjteEv8QIf_l18q4w/view)
based on the level of engagement a user has with a website, which is a model that Chrome, with the
release of Chrome 52, has moved to as well.

In both cases, it's an unknown to the developer whether they _have_ to show a notification. This
eliminates a lot of potential use-cases for the Push API. The
[Budget API](https://beverloo.github.io/budget-api/) aims to standardize this concept of budget in
a way that provides good value for developers, while not locking user agents into any particular
implementation.

Today the Budget API focuses on the Push API, but we chose to generalize the concept in order to
_(1)_ be able to provide both immediate and expected values, enabling developers to do near-term
resource planning, _(2)_ be able to extend existing APIs such as Background Sync both to alleviate
the restrictions and to enable developers to request more retries, and _(3)_ enable future resource
consuming APIs such as a Job Scheduler to use the same mechanism.

This API solely focuses on resource consuming APIs.

## Use-cases
  - Deciding to _not_ show a notification in response to a low priority push message whose primary
    purpose was to synchronize data.
  - Deciding whether the origin can schedule a precise timer using a hypothetical Job Scheduler API.
  - Deciding on the frequency of server-initiated cache updates of synchronized data.
  - Deciding on the server whether there is sufficient budget available to hide previously shown
    notifications when the user dismissed them on other devices.
  - Deciding to temporarily limit background operations if the budget could be used during an
    upcoming sporting event instead.

## Examples

### 1. Avoid showing low-priority notifications to the user.
```javascript
self.addEventListener('push', event => {
    // Execute the application-specific logic depending on the contents of the
    // received push message, for example by storing the received update.

    event.waitUntil(
        navigator.budget.reserve('silent-push').then(reserved => {
            if (reserved)
                return;  // No need to show a notification.

            // Not enough budget is available, must show a notification.
            return registration.showNotification(...);
        })
    );
});
```

### 2. Calculate the number of precise timers that can be used at some point in the future.
```javascript
function getPreciseTimersAvailableAt(time) {
    return Promise.all([
        navigator.budget.getCost('precise-timer'),
        navigator.budget.getBudget()
    ]).then(([cost, budget]) => {
        for (const state of budget) {
            if (state.time <= time)
                continue;

            // The |state| occurs after |time|, so return the budget.
            return state.budgetAt / cost;
        }

        // No budget after |time| is known, so we can't guarantee anything.
        return 0;
    });
}
```
