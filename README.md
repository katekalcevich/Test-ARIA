# Test-ARIA
Demo of accessible components. 

test-aria.html has an accessible accordion, tabs and modal component created with HTML and vanilla JavaScript. A bare minimum of CSS is used to show functionality - highlighting selected elements and to overlay the modal on screen.

The code is commented, but I've also created notes below on expected behaviour and WAI-ARIA usage.

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
