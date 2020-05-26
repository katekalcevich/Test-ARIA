# Testing ARIA
 Accessible components using ARIA. 

The file [test-aria.html](https://github.com/katekalcevich/Test-ARIA/blob/master/test-aria.html) has code for accessible accordions, tabs, notifations and modals written in HTML and JavaScript. A bare minimum of CSS is used to show functionality. The code is commented, but I've also written notes below on expected behaviour and ARIA usage.

## Landmarks
Landmarks can help screen reader users to understand the layout of a web page. You can create them using HTML elements (preferred) or ARIA roles.

| HTML Element | ARIA Role | Rules |
|---|---|---|
| &lt;header&gt; | role="banner" | Don't nest, one per page |
| &lt;nav&gt; | role="navigation" | Use labels if more than one per page |
| N/A | role="search" | Don't use on a <form> element - put it on a parent div |
| &lt;main&gt; | role="main" | Don't nest, one per page |
| &lt;aside&gt; | role="complementary" | Don't nest, use labels if more than one per page |
| &lt;section&gt; | role="region" | Use a label |
| &lt;form&gt; | role="form" | Nest inside &lt;div role="search"&gt; if it's a search form |
| &lt;footer&gt; | role="contentinfo" | Don't nest, one per page |
| N/A | role="application" | Only use if keyboard commands are handled by the application rather than the browser |

### Landmark guidelines
- Some can't be nested (see table above)
- Some must be unique to the page (see table above)
- Don't override accessibility roles - in general use ARIA roles on <div>s since they have no existing accessibility role
- No need to double up HTML and ARIA (for example, &lt;main role="main"&gt; unless you are supporting a specific browser and screen reader combination that doesn't support the landmarks you are using
- If you have multiple landmarks of the same type, use a label to distinguish them (for example, &lt;nav aria-label="Main"&gt; and &lt;nav aria-label="Education section"&gt;

### References
[HTML Sectioning Elememts](https://www.w3.org/TR/wai-aria-practices/examples/landmarks/HTML5.html)
[ARIA Landmarks](https://accessibility.oit.ncsu.edu/it-accessibility-at-nc-state/developers/accessibility-handbook/aria-landmarks/)
[Where to Put Your Search Role](https://adrianroselli.com/2015/08/where-to-put-your-search-role.html)

## Tabindex
The tabindex attribute has three distinct uses:
- tabindex="1" (or any number greater than 1) defines an explicit tab order. This is almost always a bad idea.
- tabindex="0" allows elements besides links and form elements to receive keyboard focus. It does not change the tab order, but places the element in the logical navigation flow, as if it were a link on the page.
- tabindex="-1" allows things besides links and form elements to receive "programmatic" focus, meaning focus can be set to the element through scripting, links, etc.

[Learn more about tabindex](https://webaim.org/techniques/keyboard/tabindex).

## Dropdown navigation (aka menu)
When is a menu not a menu? When you're using ARIA. 

In ARIA, a menu isa set of actions or functions. Not links. Right click anywhere on your screen right now. See the list that opens? That's a menu by ARIA definition. Don't add role="menu" to dropdown navigation. Do wrap it in a named nav landmark.

A Menu is often a list of common actions or functions that the user can invoke. The Menu role is appropriate when a list of menu items is presented in a manner similar to a menu on a desktop application.

The expected **keyboard** interaction for dropdown navigation is:
- you can open or close each item in the menu using the enter key or spacebar
- you can tab through the top level menu items and if a dropdown is open, you can tab through the links
- you can tab through all menu items and dropdown links and see the focus (Focus outlines are provided for by browsers, as long as you haven't removed them using CSS. If you removed the outline, put it back.)
- you can't tab through dropdown links if the dropdown is closed

The expected **screen reader** interaction for dropdown navigation is:
- navigation is nested inside a nav landmark with a unique name
- top level menu items are buttons (No, they don't have to look like buttons, but they should be announced as buttons since they are interactive elements. They don't link to other pages so they shouldn't be links. I've made that mistake.)
- when a dropdown is open, it's button has aria-expanded="true"
- when a dropdown is closed, it's button has aria-expanded="false"
- you won't hear dropdown links if the dropdown is closed

## Accordions
This code is adapted from [WAI-ARIA Authoring Practices](https://www.w3.org/TR/wai-aria-practices/examples/accordion/accordion.html). I modified it by:
- removing the CSS
- simplifying the HTML to only two accordion headings with a simple list inside the content panel for each
- simplifying the JS by removing the ability to control multiple accordions on one page and the ability to operate the accodions using arrow keys which is not a mandatory accessibility requirement

The expected **keyboard** interaction for accordions is:
- you can open or close an accordion using the enter key or spacebar
- you can tab through all accordions and see which accordion has the focus (Focus outlines are provided for buttons by browsers, as long as you haven't removed them using CSS. If you removed the outline, put it back.)

The expected **screen reader** interaction for accordions is:
- accordion controls are buttons (No, they don't have to look like buttons, but they should be announced as buttons since they are interactive elements. They don't link to other pages so they shouldn't be links. I've made that mistake.)
- accordion controls are also headings - these should wrap around the buttons
- when an accordion is open, it's button has aria-expanded="true" (Putting aria-expanded on the content the accordion exposes is a common error. I've done it.).
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

### aria-live="polite" or assertive vs. alerts
When you use aria-live="polite" screen readers will announce updates at the next graceful opportunity, such as at the end of a sentence. An example use case would be a success message.

When you use aria-live="assertive" screen readers will announce updates immediately. The assertive value should only be used if the interruption is critical because users need to know something immediately. 

Role="alert" is used to define for important and time sensitive messages. Using role="alert" is equivalent to using aria-live="assertive" and aria-atomic="true".

The expected **keyboard** interaction for notifications is:
- not to receive focus 

If user interaction is required, use a modal or another technique. You can move the focus to the error, but not the notification itself.

The expected **screen reader** interaction for notifications is:
- when an alert with role="alert" or aria-live="assertive" is triggered, a screen reader will say the alert right away 
- when an alert is triggered with aria-live="polite", a screen reader will say the alert when it's done reading it's current focus
- when a form is resubmitted and there are still errors, screen readers will speak remaining error messages if aria-atomic is set to true on an aria-live element

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

### Modal vs. alert dialogs
A dialog is a window overlaid on top of a web page. Users can't interact with content outside the dialog window. An alertdialog interrupts the user's workflow with an important message that requires a response from the user. For example, confirming that a user wants to cancel their task and lose all information that they entered.

The alertdialog role lets assistive technologies and browsers give alert dialogs special treatment such as playing a system alert sound.
