# Testing ARIA
Demo of accessible components. 

test-aria.html has an accessible accordion, tabs, error notifation and modal component created with HTML and JavaScript. A bare minimum of CSS is used to show functionality - highlighting selected elements and to overlay the modal on screen.

The code is commented, but I've also created notes below on expected behaviour and ARIA usage.

## Accordions
This code is adapted from [WAI-ARIA Authoring Practices](https://www.w3.org/TR/wai-aria-practices/examples/accordion/accordion.html). I modified it by:
- removing the CSS
- simplifying the HTML to only two accordion headings with a simple list inside the content panel for each
- simplifying the JS by removing the ability to control multiple accordions on one page and the ability to operate the accodions using arrow keys which is not a mandatory accessibility requirement

The expected **keyboard** interaction for accordions is:
- you can expand or collapse an accordion using the enter key or spacebar
- you can tab through all accordions and see which accordion has the focus (Focus outlines are provided for buttons by browsers, as long as you haven't removed them using CSS. If you removed the outline, put it back.)

The expected **screen reader** interaction for accordions is:
- accordion controls are buttons (No, they don't have to look like buttons, but they should be announced as buttons since they are interactive elements. They don't link to other pages so they shouldn't be links. I've made that mistake.)
- accordion controls are also headings - these should wrap around the buttons
- when an accordion is open, it's *button* has aria-expanded="true" (Putting aria-expanded on the content the accordion exposes is a common error. I've done it.).
- when an accordion is closed, it's button has aria-expanded="false"

## Tabs
This code is adapted from [ARIA Tab Panel Accessibility by Deque](https://www.deque.com/blog/a11y-support-series-part-1-aria-tab-panel-accessibility/). I've modified it by:
- allowing users to navigate the tabs using the tab key, as some keyboard users would expect (In the ARIA specs, you should only be able to navigate tabs with the arrow keys*)
- setting the focus to the selected tab's panel so that a screen reader goes right to the content

* Using arrow keys to navigate tabs allows a user to exit all the tabs using the tab key. However, this does break the expected behaviour of being able to tab through all interactive elements. In my demo, there are only three tabs. If you were building a longer list of tabs, you may want to provide a "skip link" or some type of exit functionality - perhaps with the escape key? It's a complicated problem to solve, but I don't believe we should ever make something harder for people to use by making it accessible - that's why I haven't implemented the specified keyboard interaction.

The expected **keyboard** interaction for tabs is:
- you can navigate horizontal tabs using the left or right arrow keys
- you can navigate vertical tabs using the up or down arrow keys
- you can open a tab using the enter key or spacebar
- you can exit the tabs using the tab key

I've ignored the specs for the reason noted above - it breaks usability for many people. That's bad.

What I've done instead for **keybard** interaction is:
- you can tab through all accordions and see which accordion has the focus (Focus outlines are provided for buttons by browsers, as long as you haven't removed them using CSS. If you removed the outline, put it back.)
- you can open a tab using the the enter key or spacebar

The expected **screen reader** interaction for tabs is:
- tabs are grouped inside an element (like a div) with role="tablist"
- if the group of tabs has a heading, the tablist uses aria-labelledby with the ID of the heading
- tabs have a role="tab"
- tabs use aria-controls with the ID of the element that has the tab content in it (Aria-controls isn't well supported by screen readers, but it's a useful hook for the JS that controls the tabs.)
- when a tab is open, it's button has aria-expanded="true"
- when a tab is closed, it's button has aria-expanded="false"
- the content for each tab is inside an element (like a div) with role="tabpanel" and aria-labelledby with the ID of the tab that opens it
- if the tabs are vertical, the element with role="tablist" also has aria-orientation="vertical" (horizontal is the default)

## Notifications
This code is adapted from [Simple inline error message pattern](https://developer.paciellogroup.com/blog/2016/01/simple-inline-error-message-pattern/), [User Notifications](https://www.w3.org/WAI/tutorials/forms/notifications/) and [Using ARIA role=alert or Live Regions to Identify Errors](https://www.w3.org/WAI/WCAG21/Techniques/aria/ARIA19). I've combined a variety of techniques into a single example.

### aria-live polite and assertive vs. alerts
When you use aria-live="polite" screen readers will announce updates at the next graceful opportunity, such as at the end of a sentence. An example use case would be a success message.

When you sue aria-live="assertive" screen readers will announce updates immediately. The assertive value should only be used if the interruption is critical because users need to know something immediately. 

Role="alert" is used to define for important and time-sensitive messages. 

W3C considers role="alert" equivalent to aria-live="assertive".

The expected **keyboard** interaction for notifications is:
- not to received focus 

If user interaction is required, use a modal or another technique.

The expected **screen reader** interaction for notifications is:
- when an alert with role="alert" or aria-live="assertive" is triggered, a screen reader will say the alert right away 
- when an alert is triggered with aria-live="polite", a screen reader will say the alert when it's done reading it's current focus
- when a form is resubmitted and there are still errors, screen readers will speak remaining error messages if aria-atomic is set to true

I'm not a fan of toasts. I don't think they are the best option for notifications from a usability or accessibility perspective. If you must use toast, read [Adrian Roselli's strategies for toasts](https://adrianroselli.com/2020/01/defining-toast-messages.html#Strategies).

## Modals
This code is adapted from [How To Make Modal Windows Better For Everyone](https://www.smashingmagazine.com/2014/09/making-modal-windows-better-for-everyone/). I've modified it by simplifying the CSS.

[Shepherd](https://shepherdjs.dev/) is a JavaScript library that provides an accessible guided tour using modals.

The expected **keyboard** interaction for modals is:
- when the modal opens, focus moves to an element inside the modal
- you can tab through interactive elements inside the modal, but you can't tab to anything outside the modal
- you can close the modal using the escape key
- you can close the modal by focusing on a close button and pressing the space bar or enter key
- when you close the modal, focus returns to the element that invoked the modal unless it no longer exists

The expected **screen reader** interaction for modals is:
- the modal container has a role of dialog (which screen readers will announce)
- the modal container has aria-modal set to true
- the modal has either an aria-labelledby property or an aria-label

Some screen readers will read the modal twice. I'm not aware of any way to avoid that.

### Dialogs, modal dialogs and alert dialogs
They are all interface elements that open on top of a web page. 
- A dialog requests optional input from a user - they can be cancelled. 
- Modal dialogs restrict the interface so that you can only interact with that dialog. Users must interact with the modal dialog in order to continue their task. 
- An alert dialog is used when an alert, error, or warning occurs that requires user action. 

The a modal dialog (often referred to as a modal only) and an alert dialog have different roles. A modal will also dim the web page it opens on top of, but an alert dialog won't.
