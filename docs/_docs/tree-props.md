---
docid: tree-props 
title: TreeProps
layout: docs
permalink: /docs/tree-props
---

`@TreeProp` are [props](/docs/props) which are automatically and silently passed from a parent component to its children.

`TreeProps` are a convenient way to share contextual data or utilities in a tree without having to explicitly pass `@Prop` to every component in your hierarchy. 

A good candidate, for example, is a prefetcher which fetches network images ahead of render time. The prefetcher is widely used since images are common. The prefetcher implementation might be something we define for any Component that needs to use it without having to pass it as `@Prop` in the entire tree.

### Declaring a TreeProp 

Each TreeProp is declared and created from a method annotated with @OnCreateTreeProp.

``` java
@LayoutSpec
public class ParentComponentSpec {
  @OnCreateTreeProp
  static Prefetcher onCreatePrefetcher(
      ComponentContext c,
      @Prop Prefetcher prefetcher) {
      
    return prefetcher;
  }
  
  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext c,
      @Prop Uri imageUri) {
      
    return ChildComponent.create(c)
        .imageUri(imageUri)
        .buildWithLayout();
  }
}
```

You can only declare one `@TreeProp` for any given type. If a child of ParentComponent also defines a `@TreeProp` of type Prefetcher, it will override the value of that `@TreeProp` for all its children (but not for itself).

### Using a TreeProp #

The child component can access the TreeProp value through a param annotated with `@TreeProp` that has the same type as that which was declared in the parents @OnCreateTreeProp method.

``` java
@LayoutSpec
class ChildComponentSpec {
  @OnCreateLayout
  static ComponentLayout onCreateLayout(
      ComponentContext context,
      @TreeProp Prefetcher prefetcher,
      @Prop Uri imageUri) {
    if (prefetcher != null) {
      prefetcher.prefetch(imageUri);
    }
    ...
  }
}
```
