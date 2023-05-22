---
layout: post
author: Onur Bugdayci
title: "Vaadin: Translation Oopsie"
subTitle: "A deep dive into an interesting translation issue"
date: 2023-05-22
categories: java
tags: [war-story]
img:
  url: vaadin.png
  desc: Vaadin logo
bigimg:
  url: vaadin-big.png
  desc: Vaadin alternative logo
desc: >
  A deep dive into fixing a translation issue we stumbled upon while upgrading Vaadin that through
  some frustration and out-of-the-box SOLID thinking, ultimately made my team's lives easier
toc:
  icon: icon-java
  title: Translation Oopsie
package-versions:
  - package: vaadin
    version: 24.0.4
---

At a client where I worked on a Vaadin application that had to be upgraded from Vaadin version 8 to version 22, we encountered an interesting issue regarding the way translations were done in the application.

<!--more-->

# How the translations are done

The application consisted of a bunch of views (parent components) which themselves had child components. The actual translation was done on the child component. A translation for a component is retrieved by forming a translation key for the component and using that key to check the translation table in the database for the translation value depending on the language set in the application.

**The translation key is formed as such**:

![Visual translation key formation](/assets/blog-images/vaadin_translation_key_template.png "Pretty slick")

The parent id is set on the parent component. The component type is defined by the component itself and we refer to its class name for that, for example for a checkbox component it would be checkbox. The component id is set on the component. The translation field depends on the component type. Some component types put the translation values in label fields and in that case the translation field would be “caption” (labels used to be captions in Vaadin version 8), some have the translations in div fields in which case the translation field is set as “div”. 

**An example translation key would then look something like this**:

`productview.checkbox.isavailable.caption`

The actual translation was then done by calling the static method translateLayout on the Translation utility class. This translateLayout method would take a parent component as an argument and would then loop through each child component within and for each child component it would proceed to form the translation key as defined above and do the translation by calling the static method translate which took the translation key as an argument and returned a translation.

This translateLayout method worked. Not great performance wise as a loop needed to occur before each parent component rendered with a bunch of translation calls to the database. But it didn’t have enough of an impact to warrant a refactor. Until the upgrade of course…

**translateLayout method**:
```java
public static void translateLayout(String mainComponentId, HasComponents layout) {
  for (Component component : layout) {
    translateComponent(mainComponentId, component);
  }
}

private static void translateComponent(String componentRoot, Component component) {
  String componentId = retrieveComponentId(component);
  if (Strings.isNotBlank(componentId)) {
    setComponent(componentRoot, component, componentId);
  }
}

private static void setComponent(String componentRoot, Component component, String componentId) {
  String componentClass = component.getClass().getSimpleName();

  String translation = translate(componentRoot, componentClass, componentId, ComponentType.CAPTION);
  if (translation != null) {
    component.setCaption(translation);
  }

  if (component instanceof AbstractComponent) {
    String desc = translate(componentRoot, componentClass, componentId, ComponentType.DESCRIPTION);
    if (desc != null) {
        ((AbstractComponent) component).setDescription(desc);
    }
  }

  if (component instanceof Label) {
    String value = translate(componentRoot, componentClass, componentId, ComponentType.VALUE);
    if (value != null) {
        ((Label) component).setValue(value);
    }
  }

  …etc
}
```
# The issue in the upgrade

So an interesting limitation was introduced somewhere in the versions between 8 and 22. 

**Parent components defined as a TypeScript template file in Vaadin version 22**:
```ts
@customElement('cred-vouch-design')
export class CreditVouchDesign extends LitElement {
  static get styles() {
    return css`
      :host {
        display: block;
        height: 100%;
    
    `;
  }

  render() {
    return html`
      <vaadin-vertical-layout style="width: 100%; height: 100%; padding: var(--lumo-space-m);">
        <vaadin-text-field label="IB" id="ibTextField" type="text" tabindex=""
          style="width: 100%;"></vaadin-text-field>
        <vaadin-text-field label="Bz" id="bzTextField" type="text" tabindex=""
          style="width: 100%;"></vaadin-text-field>
      </vaadin-vertical-layout>
    `;
  }

  // Remove this method to render the contents of this view inside Shadow DOM
  createRenderRoot() {
    return this;
  }
}
```

The corresponding java companion file that Vaadin creates for this template file on the server side would not allow you to access the child components within. I suppose this was done to further separate the client side from the server side of the application.

So we had 3 options to fix this:
- Tinker with the framework so child components can be accessed. This was tried and failed and wouldn’t really be ideal as this functionality might change again in a future version and would need more tinkering. Our aim was for a more permanent solution
- Implement the translation logic fully on the client side in TypeScript as from the client side we do still have access to the child components from a parent component. This was a no go as well as we had the convention of having all the logic only on the server side.
- Find a different way of translating each component.

Option 3 was the ideal way to fix this issue. The way we found to fix this issue was to do the translation from the other way around. No more using the translateLayout method. Each component would now call the existing translate method before it is rendered and do its translation. To call this translate method,  we had to create a custom component class for each Vaadin component. We then made this custom component class implement a TranslatableTitle interface that enforced the implementation of the translate method. This was a better futureproof way of handling translation as for each new component type introduced we would just need to create a new custom component class for that type and implement the TranslatableTitle interface. The old way would require us to add another if check inside the translateLayout method which wasn’t a very SOLID way of coding.

**Custom translatable component**:
```java
public class CustomCheckbox extends Checkbox implements TranslatableTitle {
  private static final String TRANSLATION_TYPE = ".checkbox.";

  public CustomCheckbox() {
    getId().ifPresent(this::translateTitle);
  }

  @Override
  public void translateTitle(String id) {
    String translation = getTranslation(parentName + TRANSLATION_TYPE + id + CAPTION_TRANSLATION_KEY)
    CustomComponentUtil.getParentName(this).ifPresent(parentName -> setLabel(translation));
  }
}

```

# Conclusion

A good way to summarize how we came to the solution for this translation issue is to say that we applied SOLID principles. More specifically the first two principles. Single responsibility principle was applied by moving the translating from the bloated translateLayout method to each component type. Open-Closed principle was applied by extending the existing Vaadin component types with our custom component type and adding our custom translation logic. 
