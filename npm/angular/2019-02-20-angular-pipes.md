---
layout: post
author: Wouter Van Schandevijl
title:  "Angular Pipes"
date:   2019-02-20 00:00:00
desc: >
    Cheat-sheet covering pretty much everything relevant
    on Angular Pipes.
img: angular-pipes.png
imgdesc:
categories: javascript
tags: [angular,cheat-sheet,tutorial]
extras:
  - githubproject: https://github.com/itenium-be/Angular-Series
interesting:
  - url: https://angular.io/generated/live-examples/pipes/stackblitz.html
    desc: Live examples from the official docs
  - url: "https://angular.io/guide/pipes"
    desc: Official Angular Pipes docs
  - url: https://angular.io/api?type=pipe
    desc: Official API docs
  - url: https://github.com/angular/angular/issues/25461
    desc: "Issue: Currency Pipe: Be able to set/change the default currency"
toc:
  title: Angular Pipes
  icon: icon-angular
---

**Pipes**: Chainable, declarative display-value transformations to use in your Html.
{: .notice}

This post covers:  
- All Builtin Angular pipes (json, async, string, array)
- How to install different locales (currency, decimal, date, percent)
- How to generate, implement and test your custom pipes
- Some examples of custom pipes ([ngx-pipes](https://github.com/danrevah/ngx-pipes) and [angular-pipes](https://github.com/fknop/angular-pipes))


An example:  
```html
<!-- This does exactly what you'd think -->
{% raw %}{{ value | uppercase }}{% endraw %}
```

<!--more-->


# Builtin Pipes

## JsonPipe <small>(Impure)</small>


Quick object dump with the **impure JsonPipe**.

**Impure**: Angular executes an impure pipe during every component change detection cycle.
An impure pipe is called often, as often as every keystroke or mouse-move.
{: .notice--info}

[JsonPipe API](https://angular.io/api/common/JsonPipe)
{: style="float: right"}

Example usage:  
```html
{% raw %}<pre>{{ {number: 7} | json }}</pre>

<!-- Combine with the AsyncPipe to dump Observables -->
<code>{{ observable$ | async | json }}{% endraw %}</code>
```

Inject in a Component:  
```
import { CurrencyPipe } from '@angular/common';

@Component({
  providers: [CurrencyPipe]
})
export class AppComponent {
  constructor(private cp: CurrencyPipe) {
    this.cp.transform(450.657, 'EUR', 'symbol', '0.2-2', 'fr'); // 450,66 €
  }
}
```

## String Casing Pipes

[UpperCasePipe](https://angular.io/api/common/UpperCasePipe)
, [LowerCasePipe](https://angular.io/api/common/LowerCasePipe)
and [TitleCasePipe](https://angular.io/api/common/TitleCasePipe).

```html
{% raw %}lowercase => {{ 'LOWERCASE' | lowercase }}

UPPERCASE => {{ 'uppercase' | uppercase }}

This Is Title Case => {{ 'tHIs is tiTLE CaSe' | titlecase }}{% endraw %}
```

## Locale Sensitive Pipes

Used by **DatePipe**, **CurrencyPipe**, **DecimalPipe** and **PercentPipe**.


### Setup

[Official i18n docs](https://angular.io/guide/i18n#setting-up-the-locale-of-your-app)
{: style="float: right"}

Angular comes with the `en-US` locale only.
Import a different locale in your `app.module.ts`:

```typescript
import { registerLocaleData } from '@angular/common';
import { LOCALE_ID, NgModule } from '@angular/core';
import localeFr from '@angular/common/locales/fr';

registerLocaleData(localeFr, 'fr');

@NgModule({
  providers: [{provide: LOCALE_ID, useValue: 'fr'}],
})
export class AppModule { }
```



### CurrencyPipe

[CurrencyPipe API](https://angular.io/api/common/CurrencyPipe)
{: style="float: right"}

Default currency is `$`, this can as of yet not be set globally.
Consider using a [Money](https://martinfowler.com/eaaCatalog/money.html) class instead.

```html
{% raw %}<!-- Signature -->
{{ value_expression | currency [ : currencyCode [ : display [ : digitsInfo [ : locale ] ] ] ] }}

<!-- Examples -->
€1,500.95 => {{ 1500.953 | currency:'EUR':'symbol' }}

003,96 EUR => {{ 3.955 | currency:'EUR':'code':'3.2-2':'fr' }}{% endraw %}
```

Parameters:  
- currencyCode: USD, EUR, GBP, ... ([full list](https://en.wikipedia.org/wiki/ISO_4217#Active_codes))
- display: code=USD, symbol=$, a string
- digitsInfo: {% raw %}[minIntegerDigits=1].[minFractionDigits=2]-[maxFractionDigits=2] (ex: 3.2-2 -> 000.00){% endraw %}
- locale: en-US

Parameters `digitsInfo` and `locale` work exactly the same for DecimalPipe and PercentPipe.


[DecimalPipe API](https://angular.io/api/common/DecimalPipe)
{: style="float: right"}

### DecimalPipe


```html
{% raw %}<!-- Signature -->
{{ value_expression | number [ : digitsInfo [ : locale ] ] }}

<!-- Example -->
025.1230 => {{ 25.123 | number:'3.4-4'}}{% endraw %}
```

[PercentPipe API](https://angular.io/api/common/PercentPipe)
{: style="float: right"}

### PercentPipe

```html
{% raw %}<!-- Signature -->
{{ value_expression | percent [ : digitsInfo [ : locale ] ] }}

<!-- Example -->
26% => {{ 0.259 | percent }}{% endraw %}
```


### DatePipe

[DatePipe API](https://angular.io/api/common/DatePipe)
{: style="float: right"}

Works on a `Date`, a number (ms since UTC epoch), or an [ISO string](https://www.w3.org/TR/NOTE-datetime)

```html
{% raw %}<!-- Signature -->
{{ value_expression | date [ : format [ : timezone [ : locale ] ] ] }}

<!-- Basic examples -->
May 1, 2019 => {{ Date.parse('2019-05-01') | date }}
2019-05-01 23:55:00 => {{ Date.parse('2019-05-01T23:55:00') | date:'yyyy-MM-dd HH:mm:ss' }}

<!-- Full blown example -->
1 mai 2019, 21h00 => {{ Date.parse('2019-05-01T23:55:00') | date:"d MMMM yyyy, HH'h'mm":'-0055':'fr' }}{% endraw %}
```

More options for the format:  
- Escape characters by placing them inside single quotes
- **Week of year**: w=7, ww=07
- **Week day**: E=Tue, EEEE=Tuesday, EEEEE=T, EEEEEE=Tu
- **Month** standalone: LLLL=September
- **Period**: a=am/pm
- **Hour 1-12**: h=1, hh=01
- **Zone**: z=GMT-8, Z=-0800
- Or named: short, medium, long, full; shortDate, mediumDate (default), longTime, ...







## Array Pipes

[SlicePipe API](https://angular.io/api/common/SlicePipe)
{: style="float: right"}

### SlicePipe <small>(Impure)</small>

```html
{% raw %}<!-- Signature -->
{{ value_expression | slice : start [ : end ] }}

<!-- Examples -->
[ 0, 1 ] => {{ [0, 1, 2, 3, 4] | slice:0:2 | json }}
  
[ 4 ] => {{ [0, 1, 2, 3, 4] | slice:-1 | json }}

[ 0, 1 ] => {{ [0, 1, 2, 3, 4] | slice:-5:2 | json }}

[ 0, 1, 2 ] => {{ [0, 1, 2, 3, 4] | slice:-5:-2 | json }}{% endraw %}
```



### KeyValuePipe <small>(Impure)</small>

[KeyValuePipe API](https://angular.io/api/common/KeyValuePipe)
{: style="float: right"}

Works on Objects and Maps.

```html
<div *ngFor="let item of object | keyvalue:compareFn">
    {% raw %}{{item.key}} => {{item.value}}{% endraw %}
</div>
```

The optional `compareFn` parameter signature defined in `component.ts`:
```typescript
export class AppComponent {
    object = {key1: 1, key2: 2};

    // revert sorting of this.object keys
    // interface KeyValue<K, V>{key: K, Value: V}
    compareFn(a: KeyValue<string, number>, b: KeyValue<string, number>) {
        return b.key.localeCompare(a.key);
    }
}
```

The output would be:

- `let item of object | keyvalue:compareFn`: `key2 => 2` and `key1 => 1`
- `let item of object | keyvalue`:  `key1 => 1` and `key2 => 2`







## AsyncPipe <small>(Impure)</small>

[AsyncPipe API](https://angular.io/api/common/AsyncPipe)
{: style="float: right"}

Returns the last emitted value. Unsubscribes automatically in `ngOnDestroy`.

```html
{% raw %}Time: {{ time$ | async }}

<!-- Capture the value without re-evaluating time$ -->
<div *ngIf="time$ | async as time; else loading">
    {{ time }}
</div>
<ng-template #loading>
    Waiting...
</ng-template>{% endraw %}
```

Where `time$` is an Observable or a Promise. Ex:
```typescript
export class AppComponent {
  time$ = new Observable<string>(observer => {
    setInterval(() => observer.next(new Date().toString()), 1000);
  });
}
```



# Custom Pipes

Create your own pipes for displaying values relevant in your application domain.

## Creation

Use [`ng generate`](https://angular.io/cli/generate#pipe)

```powershell
ng g pipe <name> options
```

Options:  
- `--export=false`: Add to module declarations and exports?
- `--flat=true`: Write at the top level of the project
- `--lintFix=false`
- `--module=` and `--project=`
- `--skipImport=false` and `--skipTests=false`

## Example

```typescript
import { Pipe, PipeTransform } from '@angular/core';

{% raw %}// Usage: 
// 15:30 => {{ someDate | hours }}
// 15h => {{ someDate | hours:true:false  }}{% endraw %}

@Pipe({name: 'hours', pure: true})
export class HoursPipe implements PipeTransform {
  transform(value: Date, showHours = true, showMinutes = true): string {
    value = new Date(value);
    if (showMinutes) {
        return value.getHours() + ':' + value.getMinutes();
    }
    return value.getHours() + 'h';
  }
}
```

## Installation

If you didn't use `ng generate`.

```typescript
@NgModule({
  declarations: [AppComponent, CustomPipe],
  exports: [CustomPipe] // Also add to `exports` when declaring in a shared module.
})
```


## Testing

Run tests with `ng test`.

```typescript
import { HoursPipe } from './hours.pipe';

describe('HoursPipe', () => {
  const someDate = new Date('2000-01-01T15:30:00');
  let pipe: HoursPipe;

  beforeEach(() => {
    pipe = new HoursPipe();
  });

  it('shows h:M by default', () => {
    const result = pipe.transform(someDate);
    expect(result).toBe('15:30');
  });

  {% raw %}it('shows 15h with arguments {{ value | hours:true:false }}', () => {
    const result = pipe.transform(someDate, true, false);
    expect(result).toBe('15h');
  });{% endraw %}
});
```


# Real World Examples

Some custom examples to get in the right mind what Pipes could be useful for.

## Nl2brPipe

Convert newlines in string resources to `<br>`s.

```typescript
import { Pipe, PipeTransform } from '@angular/core';

// Usage: <div [innerHTML]="'Line1\nLine2' | nl2br"></div>

// Combine with ngx-translate
// Usage: <div [innerHTML]="'resource.key' | translate | nl2br"></div>

@Pipe({name: 'nl2br'})
export class Nl2brPipe implements PipeTransform {
  transform(value: string): string {
    return value.replace(/\n/g, '<br />');
  }
}
```

## UCFirstPipe

While `capitalize` (capitalize each word) is bundled with Angular,
one to capitalize just the first letter is not.

```typescript
@Pipe({ name: 'ucfirst' })
export class UcFirstPipe implements PipeTransform {
   transform(text: any): string {
     if (typeof text !== 'string') {
       return text;
     }
    return text.slice(0, 1).toUpperCase() + text.slice(1);
  }
}
```

## BytesPipe

Fancy display of filesizes etc.

```typescript
// Usage: {{ 1024 | bytes }}

@Pipe({ name: 'bytes' })
export class BytesPipe implements PipeTransform {
  private dict: Array<{max: number; type: string}> = [
    { max: 1024, type: 'B' },
    { max: 1048576, type: 'KB' },
    { max: 1073741824, type: 'MB' },
    { max: 1.0995116e12, type: 'GB' },
  ];

  transform(value: number, precision: number = 0): string | number {
    if (isNaN(parseFloat(String(value))) || !isFinite(value)) {
      return NaN;
    }

    const format = this.dict.find(d => value < d.max) || this.dict[this.dict.length - 1];
    const calc = value / (format.max / 1024);
    const num = this.applyPrecision(calc, precision);

    return `${num}${format.type}`;
  }

  applyPrecision(num: number, precision: number) {
    if (precision <= 0) {
      return Math.round(num);
    }

    const tho = 10 ** precision;
    return Math.round(num * tho) / tho;
  }
}
```



## ngx-pipes

Pretty much any generic pipe you can think of already exists.
This is one project with such collection.

{% include github-stars.html url="danrevah/ngx-pipes" %}


```cmd
npm install ngx-pipes --save
```

```typescript
import { NgPipesModule } from 'ngx-pipes';

// Each pipe is also exposed separately
// ex: import { ReversePipe, LeftTrimPipe } from 'ngx-pipes';

@NgModule({
  imports: [NgPipesModule]
})
```

**String:**
[shorten](https://github.com/danrevah/ngx-pipes/blob/master/src/pipes/string/shorten.ts)
, [slugify](https://github.com/danrevah/ngx-pipes/blob/master/src/pipes/string/slugify.ts)
, [lpad](https://github.com/danrevah/ngx-pipes/blob/master/src/pipes/string/lpad.ts)
, [camelize](https://github.com/danrevah/ngx-pipes/blob/master/src/pipes/string/camelize.ts)

**Array:**
[unique](https://github.com/danrevah/ngx-pipes/blob/master/src/pipes/array/unique.ts)
, [tail](https://github.com/danrevah/ngx-pipes/blob/master/src/pipes/array/tail.ts)
, [flatten](https://github.com/danrevah/ngx-pipes/blob/master/src/pipes/array/flatten.ts)
, [every](https://github.com/danrevah/ngx-pipes/blob/master/src/pipes/array/every.ts)
, ...

**Other:**
[isGreaterThan](https://github.com/danrevah/ngx-pipes/blob/master/src/pipes/boolean/is-greater-than.ts)
, ...



## angular-pipes

Pretty much the same collection of pipes in this project.

{% include github-stars.html url="fknop/angular-pipes" %}


```cmd
npm install angular-pipes --save
```

```typescript
import { NgModule } from '@angular/core';

import {
    NgAggregatePipesModule, NgArrayPipesModule, 
    NgBooleanPipesModule, NgMathPipesModule,
    NgObjectPipesModule, NgStringPipesModule,
} from 'angular-pipes';

// Import all pipes
// import { NgPipesModule } from 'angular-pipes';

@NgModule({
    imports: [
        NgArrayPipesModule,
        NgStringPipesModule,
    ]
})
export class MyApplicationModule {}
```


**String:**
[decodeURI](https://github.com/fknop/angular-pipes/blob/master/src/string/decode-uri.pipe.ts)
, [latinize](https://github.com/fknop/angular-pipes/blob/master/src/string/latinize.pipe.ts)
, [repeat](https://github.com/fknop/angular-pipes/blob/master/src/string/repeat.pipe.ts)
, [replace](https://github.com/fknop/angular-pipes/blob/master/src/string/replace.pipe.ts)
, [reverseStr](https://github.com/fknop/angular-pipes/blob/master/src/string/reverse-str.pipe.ts)
, [truncate](https://github.com/fknop/angular-pipes/blob/master/src/string/truncate.pipe.ts)
, [wrap](https://github.com/fknop/angular-pipes/blob/master/src/string/wrap.pipe.ts)
, [stripTags](https://github.com/fknop/angular-pipes/blob/master/src/string/strip-tags.pipe.ts)
, ...

**Array:**
[max](https://github.com/fknop/angular-pipes/blob/master/src/aggregate/max.pipe.ts)
, [min](https://github.com/fknop/angular-pipes/blob/master/src/aggregate/min.pipe.ts)
, [sum](https://github.com/fknop/angular-pipes/blob/master/src/aggregate/sum.pipe.ts)
, [count](https://github.com/fknop/angular-pipes/blob/master/src/array/count.pipe.ts)
, [head](https://github.com/fknop/angular-pipes/blob/master/src/array/head.pipe.ts)
, ...

**Number / Math:**
[bytes](https://github.com/fknop/angular-pipes/blob/master/src/math/bytes.pipe.ts)
, [degrees](https://github.com/fknop/angular-pipes/blob/master/src/math/degrees.pipe.ts)
, [ceil](https://github.com/fknop/angular-pipes/blob/master/src/math/ceil.pipe.ts)
, [round](https://github.com/fknop/angular-pipes/blob/master/src/math/round.pipe.ts)
, [sqrt](https://github.com/fknop/angular-pipes/blob/master/src/math/sqrt.pipe.ts)
, ...

**Other:**
[defaults](https://github.com/fknop/angular-pipes/blob/master/src/object/defaults.pipe.ts)
, ...
