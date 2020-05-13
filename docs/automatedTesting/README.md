# Automated testing guidelines

IN THIS DOC

- Automated testing
    - [Browser Tests](#Browser-tests)

<br/>
<hr/>
<br/>

# Browser tests

We have setup [Selenium](https://selenium.dev) to afford testing specific functionality of the Memex web extension
in the latest stable versions of Chrome and Firefox.

## Writing tests

Foundations for writing tests have been laid and currently live in [`src/tests/selenium/`](https://github.com/WorldBrain/Memex/blob/5a6538d2f8464f1690900271aa58cbd0d1b06c4a/src/tests/selenium) modules:

- `/helpers`: common helper functions, like locating elements in our shadow DOM or triggering the ribbon to show with the mouse
- `/setup`: common setup functions, like setting up and loading specific browser drivers with the extension
- `/selectors/`: CSS selectors, built incrementally from DOM/shadow DOM root elements, organized by screen

It's recommended to look at some of the [example tests](#Example-tests) to see how they use these foundation modules.


### CSS Selectors

The main foundations that are likely to be commonly interacted with and updated are the CSS selectors.
As new tests are written, there will likely be a need to specify new selectors to interact with different parts of the page/s.

Any new selectors should go in an appropriately named module in `src/tests/selenium/selectors` corresponding to the page on which they are used.<br/>
e.g., anything on the overview goes in `overview.ts`, in-page sidebar or ribbon goes in `sidebar.ts`.

It is important that any new selectors for new elements build on top of any existing selectors, rather than respecifying the selector path up until that point.
This avoids needing to change multiple selectors as the DOM changes.

_**NOTE:** I am unsure if this idea with organizing the selectors like this will scale - the only experience I have where I've employed a similar solution for a similar problem is managing Redux state selectors with [`reselect`](https://www.npmjs.com/package/reselect). It's more of a suggestion for now, and beats keeping selectors as string literals in inidividual tests. Very open to other suggestions._

## Caveats

1. If not using the main `makeTestFactory` setup function, ensure
[`WebDriver.prototype.quit`](https://www.selenium.dev/selenium/docs/api/javascript/module/selenium-webdriver/index_exports_WebDriver.html#quit)
is manually called at the end of your test.

1. Selectors for common elements will need to change as the extension markup changes. This should be obvious when it
happens as the tests will start to fail.<br/>
_This could be mitigated by adding more IDs at important points of our markup, and having selectors start from there rather than the root element of the page._

## Example tests

1. Bookmarking a page via the in-page ribbon then searching for it via the overview:
    [`src/bookmarks/index.browser-test.ts`](https://github.com/WorldBrain/Memex/blob/5a6538d2f8464f1690900271aa58cbd0d1b06c4a/src/bookmarks/index.browser-test.ts)

1. Creating a note on a page via the in-page ribbon then searching for it via the in-page sidebar:
    [`src/annotations/index.browser-test.ts`](https://github.com/WorldBrain/Memex/blob/5a6538d2f8464f1690900271aa58cbd0d1b06c4a/src/annotations/index.browser-test.ts)

## Useful links

1. `selenium-webdriver` API docs: https://www.selenium.dev/selenium/docs/api/javascript/index.html
