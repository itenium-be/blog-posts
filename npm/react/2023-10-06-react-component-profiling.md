---
layout: post
author: Van Schandevijl Wouter
title: "React Component Profiling"
subTitle: "Extensive guide on React performance optimalization in a real-world application"
date: 2023-10-06
categories: javascript
tags: [react,war-story]
img:
  url: react-profiling.png
  desc: Photo by Midjourney
  prompt: "a wristwatch with inside the react logo"
bigimg:
  url: react-profiling-big.png
  origin: Midjourney
  prompt: "the fastest thing in the world"
desc: >
  War Story on improving the performance of "confac",
  our internal invoicing application.
extras:
  - githubproject: https://github.com/itenium-be/React-ComponentProfiling
    githubtext: "Presentation source materials (pptx, code snippets, ...)"
  - githubproject: https://github.com/itenium-be/confac
    githubtext: "confac Github source"
interesting:
  - git: amsterdamharu/selectors
    desc: "Examples of reselect selectors"
toc:
  icon: icon-reactjs
  title: Component Profiling
socials:
  linkedin: "https://www.linkedin.com/posts/itenium_react-component-profiling-activity-7187027631463145472-sc57?utm_source=share&utm_medium=member_desktop"
  twitter: "https://twitter.com/itenium_be/status/1781261551279677493"
package-versions:
  - package: react
    version: 18.2.0
---

Serious rendering performance troubles in [confac](https://github.com/itenium-be/confac),
our internal React invoicing app. It reads the entire database in memory 😲 and works
pretty much exclusively on the frontend with the Redux store, so it's not the db that
is the performance bottleneck for once 😜

Worst case scenario on the `/monthly-invoicing` page is a whopping 10s wait time for
a single screen to become responsive. Not good.


<!--more-->


# React Developer Tools

The [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en) Chrome extension adds two tabs to DevTools:

- ⚛️ Components: like Inspect Element but with React tags
- ⚛️ Profiler: the simple and excellent React Component Profiler


## Profiler Tab

### Action Buttons

- <i class="fa fa-circle" style="color: #0086FC"></i> / <i class="fa fa-circle" style="color: #FC3A4B"></i> : Start / Stop profiling
- <i class="fa fa-refresh" style="color: #59616C"></i> : Reload page and start profiling
- <i class="fa fa-ban" style="color: #59616C"></i> : Clear profiling data
- <i class="fa fa-arrow-left" style="color: #59616C"></i> / <i class="fa fa-arrow-right" style="color: #59616C"></i> : Select previous / next commit


### Display

- <i class="fa fa-fire" style="color: #0086FC"></i> : Flamegraph chart
- <i class="fa fa-sort-amount-desc" style="color: #0086FC"></i> : Ranked chart


### Interesting Settings

Filter the output once it becomes unwieldly in the <i class="fa fa-cog" style="color: #59616C"></i> settings modal:

- `Components > Hide components where...`: custom result filtering based on DOM type (ex: div), name, ...
- `Profiler > Hide commits below x ms`: ignore very fast renders (ex: rerenders for tooltips)


# Using the Profiler

Press the "<i class="fa fa-circle" style="color: #0086FC"></i> Start profiling" button,
perform the action(s) that take too long and press the "<i class="fa fa-circle" style="color: #FC3A4B"></i> Stop profiling"
button. Or use "<i class="fa fa-refresh" style="color: #59616C"></i> : Reload page and start profiling" when it's your
initial page load that is taking too long.

The default "<i class="fa fa-fire" style="color: #0086FC"></i> : Flamegraph chart" display
is perfect as long as the next performance issues are easy to pick out.


{% include post/image.html file="rcp-profiler-flamegraph.png" alt="" title="" desc="So what is this 'InvoiceWorkedDays' component?" maxWidth="650px" %}


# Easy Pickings

⏱️ 6 seconds for 100 invoices/rows.

The first three profiling rounds revealed that the biggest issues were in non-component code:

- **Third Party Packages**: Do not assume that free, open-source, packages will be performant.
- **Nested Loops**: Several inner loops caused a significant CPU-bound delay.
- **Frequent I/O**: `sessionStorage` was being accessed for each component wrapped in a role check.


<br>

## Culprit 1: Third Party Package

`InvoiceWorkedDays`, the component in confac that was causing all this fuzz:

{% include post/image.html file="rcp-confac-invoice-list.png" alt="" title="" desc="An entire second spent on displaying an amount of days" maxWidth="650px" %}

The part that was eating up all this time was [`date-holidays`](https://github.com/commenthol/date-holidays),
an npm package used to calculate the official Belgian holidays.

The date-holidays can calculate holidays for Chinese, Korean and every other calendar out in existence.
Because of the un-tree-shakable initialization `const hd = new Holidays('BE');` all the code for those
calendars were also being parsed.
Sub-dependencies included [`astronomia`](https://github.com/commenthol/astronomia),
leading to a footprint of about 10MB.

We have since replaced the dependency with our own, dedicated (76 liner), npm package
[`@itenium/date-holidays-be`](https://github.com/itenium-be/date-holidays-be).

<br>

## Culprit 2: Nested Loops (CPU)

{% include post/image.html file="rcp-profiler-flamegraph-InvoiceDownloadIcon.png" alt="" title="" desc="Another unexpected performance optimalization target: the InvoiceDownloadIcon" maxWidth="650px" %}

```ts
// InvoiceDownloadIcon
const fullProjectMonth = useProjectMonthFromInvoice(invoice._id);
```

This custom Hook would first make a `FullProjectMonth` of all 'projectMonth' records
in the Redux store and then pick the one with `invoice._id`.

Problem here was, that to resolve a "projectMonth", 5 other collections needed to be
traversed as well. As the data grew, time spent here grew considerably.

```ts
// fn mapToProjectMonth
const project = confacState.projects.find(p => p._id === projectMonth.projectId);
const consultant = confacState.consultants.find(c => c._id === project.consultantId);
const client = confacState.clients.find(c => c._id === project.client.clientId);
const partner = confacState.clients.find(c => project.partner && c._id === project.partner.clientId);
const invoice = confacState.invoices.find(i => i.projectMonth && i.projectMonth.projectMonthId === projectMonth._id);
```

We can greatly optimize this by first filtering on `invoice._id` so the 5 inner loops are executed just once!

<br>

## Culprit 3: I/O (sessionStorage)

⏱️ 2 seconds for 100 invoices/rows.

{% include post/image.html file="rcp-profiler-flamegraph-WithClaim.png" alt="" title="" desc="And now the WithClaim decorator??? What is happening there..." maxWidth="628px" %}


It's starting to become difficult to discern the next performance culprit.
The second profiler display method
"<i class="fa fa-sort-amount-desc" style="color: #0086FC"></i> : Ranked chart" to the rescue!


{% include post/image.html file="rcp-profiler-WithClaim-ranked-graph.png" alt="" title="" desc="Profiler Ranked chart display" maxWidth="650px" %}

#### The Code

```ts
// The decorator
export const EnhanceWithClaim = <P extends object>(Component: React.ComponentType<P>) =>
  class WithClaim extends React.Component<P & EnhanceWithClaimProps> {
    render() {
      const {claim, ...props} = this.props;
      if (claim) {
        const claims: string[] = authService.getClaims();
        if (!claims.includes(claim)) {
          return null;
        }
      }
      return <Component {...props as P} />;
    }
  };

// Wrap a component
const LinkWithClaim = EnhanceWithClaim(Link);

// Usage
const CreateInvoiceLink = () => (
  <LinkWithClaim claim="invoice-create" to="/invoices/create">
    Create Invoice
  </LinkWithClaim>
)
```

The culprit here turned out to be `authService.getClaims()`,
which ended up doing the following for every of the hundreds
of icons, buttons etc displayed.

```ts
// I/O being executed many, many times == performance impact!
const token = sessionStorage.getItem('jwt');
```

<br>

## Alternatives

When you are rendering a grid with thousands of rows,
you'll always going to end up with a performance problem (at some point).

There are several mitigations:

- **Pagination** <i class="fa fa-check-circle" style="color: #AFE1AF"></i> Implemented!
- **Partial data load** <small>(only load last year worth of data in memory at startup)</small> <i class="fa fa-check-circle" style="color: #AFE1AF"></i> Implemented!
- **Debouncing**: Useful when filtering the grids, only start a rerender when the user is done entering the filter criteria
- **Virtualization & Infinite Scroll**: A grid can display an "infinite" amount of rows and still be performant!
- **Suspense & useDeferredValue**: Not relevant here because all data is already in memory


## Conclusions

⏱️ 360 ms for 100 invoices/rows.

These were the "easy pickings", where the profiler's flamegraph gave it to us straight.
This greatly improved performance (6s -> 360ms) which is good enough.

- Not doing a calculation or render is the best optimalization
- Keep an eye on 3rd party packages
- I/O operations are expensive


<br>

# The Meaty Part

There is another screen and performace there is... disastrous.

- Everything is re-rendered whenever a projectMonth gets collapsed/opened.
    - Opening more and more projectMonths gets slower and slower.
    - ⏱️ 2 open projectMonths: 350ms
    - ⏱️ 4 open projectMonths: 1.2s
    - ⏱️ 18 open projectMonths: 5s
- Filters are remembered between route changes.
    - Navigating back can be painfully slow when many projectMonths were open.
    - ⏱️ With 18 open projectMonths: 4.4s


{% include post/image.html file="rcp-confac-projectMonths.png" alt="" title="" desc="The projectMonths screen -- open up more months and you can go get a coffee in between renders 😢" maxWidth="650px" %}

<br>

## Belgian Holidays Cache

For this workshop, `date-holidays` was not replaced with a more dedicated and efficient alternative.
Instead we're using it as a placeholder for something that would actually take a long time
and cannot be so easily improved.

To get to the following flamegraph, we added a simple cache for the `date-holidays` dependency.

```ts
const workDaysInMonthCache: {[month: string]: number} = {};
```

{% include post/image.html file="rcp-profiler-flamegraph-projectMonths.png" alt="" title="" desc="A more even distribution now... So no easy performance targets" maxWidth="650px" %}


## Avoiding IFeature Construction

The `IFeature` configures a grid and CRUD pages for a backend entity.

It contains the data and currently active filters. The problem here
is that the `openMonths: string[]` filter is also part of the data
of the `IFeature`.

Because of how Redux works, the `openMonths: string[]` filter becomes a new
array reference each time a projectMonth is opened/collapsed. This causes
all months to be re-rendered instead of just the one being toggled
because the filters object has changed.

Furthermore, the expensive `IFeature` construction is not necessary
when the projectMonth is collaped. Instead we can map directly to
`CollapsedProjectMonthBadgeTotals`, which doesn't include any inner loops.


```ts
export const ProjectMonthsList = ({feature, month}: ProjectMonthsListProps) => {
  // If this isn't a Demeter violation, I don't know what is 😓
  if (feature.list.filter.state.openMonths.includes(month)) {
    return (
      <>
        <Toolbar feature={feature} />
        <List feature={feature} />
      </>
    );
  }

  // When the ProjectMonth is collapsed, we don't
  // really need to construct the IFeature...
  return <ProjectMonthListCollapsed feature={feature} />;
};
```

<br>

## Introducing: Reselect

A library for creating [memoized "selector" functions](https://github.com/reduxjs/reselect).
Commonly used with Redux and comes with the [@reduxjs/toolkit](https://redux-toolkit.js.org/).

Caching the expensive mapping to `FullProjectMonthModels` & `CollapsedProjectMonthBadgeTotals`
with `createSelector`:

```ts
// Create the selector
import { createSelector } from 'reselect';

export const createFullProjectMonthsSelector = () => createSelector(
  // Select required store data
  // Rerenders happen when these references change
  (state: ConfacState) => state.projectsMonth,
  (state: ConfacState) => state.projects,
  (state: ConfacState) => state.consultants,

  // Introduce a "month" parameter for the selector
  (_, month: string) => month,

  // The actual mapping
  (projectsMonth, projects, consultants, month) => {
    const context = { projectsMonth, projects, consultants }
    return projectsMonth
      .filter(x => isSameMonth(x.month, month))
      .map(x => mapToProjectMonth(context, x));
  },

  // Optional configuration
  {
    memoizeOptions: {
      equalityCheck: (a, b) => a === b, // ex: use deep-equal instead
      maxSize: 10, // selector cache
      // import { shallowEqual } from 'react-redux';
      resultEqualityCheck: shallowEqual
    }
  }
);
```

<br>

## Introducing: useMemo

Skip expensive recalculations with [`useMemo`](https://react.dev/reference/react/useMemo)

```ts
useMemo(calculateValue, dependencies)
```


We now need to cache the `createFullProjectMonthsSelector` mapped data
for each month. Because we want to keep a memoized version of each month,
we pass an empty array as the dependencies argument to useMemo.

If you need to cache only until the month changes, as in when you are
navigating from one detail page to another, the dependencies would become
`[month]`.


```ts
// Selector usage
import { useMemo } from 'react';

export const OpenedProjectMonthsList = ({ month }: OpenedProjectMonthsListProps) => {
  // Use the createSelector as defined above
  // With empty array as dependencies argument
  const selectProjectMonths = useMemo(createFullProjectMonthsSelector, []);

  // Use the memoized selector
  const projectMonths = useSelector(state => selectProjectMonths(state, month));

  // And construct the IFeature
  return <List feature={projectMonthFeature(projectMonths)} />;
};
```

<br>

## Introducing: memo

When a `ProjectMonthListCollapsed` is rendered, its details don't change,
so we can do some aggressive caching there, keeping the entire rendered
component in the cache with [`React.memo(component, propsAreEqual?)`](https://react.dev/reference/react/memo).


```ts
import { memo } from 'react';

// Memoize the component for each value of month
const ProjectMonthListCollapsedMemo = memo(({ month }) => {
  return <ProjectMonthListCollapsed month={month} />;
});


export const OpenOrClosedProjectMonthsList = ({ month }) => {
  const isOpen = useSelector(state => state.filters.projectMonths.openMonths[month]);
  if (isOpen) {
    return <OpenedProjectMonthsList month={month} />;
  }
  return <ProjectMonthListCollapsedMemo month={month} />;
};
```

<br>

## The Aftermath


{% include post/image.html file="rcp-profiler-projectMonths-final.png" alt="" title="" desc="Flamegraph after opening a second projectMonth" maxWidth="650px" %}

1. No re-render of the previously opened projectMonth (reselect & useMemo)
2. The render of the newly opened projectMonth
3. The completely cached collapsed/closed projectMonths (memo)


## Conclusions

- ⏱️ 2 projectMonths: 350ms -> 170ms
- ⏱️ 4 projectMonths: 1.2s -> 311ms
- ⏱️ 18 open projectMonths: 5s -> 140ms

The biggest win was avoiding the re-renders of all projectMonths
when opening/closing just one and caching expensive Redux selects.

### Take-Aways

- Depend on as little as possible
  - Components should rely on their minimum requirements of state and props only
  - Prefer props only for low level components
- Cache expensive calculations
- createSelector, useMemo and memo are your friends

#### The IFeature

> _"Weeks of coding can save you hours of planning."_
> — Fred Brooks


### Unexpected Side-Effect

The overal structure and code quality has improved due the refactorings
needed to realize the performance targets.

- More components with finer grained purpose resulted in a clearer component hierarchy
- Complexity has increased but it is hidden away in hooks and selectors



# Profiler Alternatives

If the Chrome Profiler Extension isn't cutting it for you anymore,
you can still fallback to these alternatives for locating
performance optimalization targets.


## console.time

Good old [console](https://developer.mozilla.org/en-US/docs/Web/API/console/timeEnd) 😉

```ts
console.time('timer1')

const result = expensiveOp1()
console.timeLog('timer1', 'result is', result)
expensiveOp2()

console.timeEnd('timer1')
```

## Profiler Component

[`<Profiler />`](https://react.dev/reference/react/Profiler) doesn't actually render anything
and can be nested by providing different ids.

```ts
import { Profiler } from 'react';

function callback(
  id, // the "id" prop of the Profiler tree that has just committed
  phase, // either "mount" (if the tree just mounted) or "update" (if it re-rendered)
  actualDuration, // time spent rendering the committed update
  baseDuration, // estimated time to render the entire subtree without memoization
  startTime, // when React began rendering this update
  commitTime, // when React committed this update
  interactions // the Set of interactions belonging to this update
) {
  console.log(`${id}: ${phase} in ${actualDuration}`);
}

const LongRendering = () => (
  <Profiler id="uniqueId" onRender={callback}>
    <LongRenderingItem />
  </Profiler>
)
```

For a simple case like this with only one `<Profiler`, you are better off just using
the Chrome extension.

It can come in handy for micro optimizations, using multiple `<Profiler />`s
and hundreds/thousands of components. At that point you will need to write your
own aggregator in the callback to make some sense of the data.

A simple aggregator on `ID`:

```ts
type ITiming = {
  renders: number;
  totalTime: number;
  averageTime?: number;
}

const timings: {[key: string]: ITiming} = {}

function callback(id, phase, actualDuration) {
  if (!timings[id]) {
    timings[id] = {renders: 1, totalTime: actualDuration};
  } else {
    const oldTimings = timings[id];
    timings[id] = {
      renders: oldTimings.renders + 1,
      totalTime: oldTimings.totalTime + actualDuration
    };
  }

  timings[id].averageTime = timings[id].totalTime / timings[id].renders;
}
```


# Further Optimalizations

- Avoid inline styles
- [**useCallback**](https://react.dev/reference/react/useCallback): cache event handlers
  - Tricky: useCallback also takes time; in my case it actually degraded performance
  - As always with perf optimizations --> **MEASURE** (the profiler is your friend)
- **memo**: Components without props can be cached entirely
- [**useWhyDidYouUpdate**](https://github.com/itenium-be/React-ComponentProfiling/blob/master/snippets/useWhyDidYouUpdate.ts): part of many hooks-libraries (I didn't find it very usefull)
- [**useDebugValue**](https://react.dev/reference/react/useDebugValue): to be used in custom hooks (more for library developers IMO)
