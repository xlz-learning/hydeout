---
layout: post
title: 100 Days Of SwiftUI - Aug 24 - 29
categories:
    - book
excerpt_separator:  <!--more-->
---

## [Project 13, part 1](https://www.hackingwithswift.com/100/swiftui/62)

### How property wrappers become structs

If we take a look at the definition of `State`

You see something like the following

```swift
@propertyWrapper public struct State<Value> : DynamicProperty {
...
public var wrappedValue: Value { get nonmutating set }
...
}
```

The `wrappedValue` is the actual value we're trying to store.

```swift
@State private var blurAmount: CGFloat = 0 {
    didSet {
        print("New value is \(blurAmount)")
    }
}
```

On the surface, that states “when `blurAmount` changes, print out its new value.” However, because `@State` actually wraps its contents, what it’s actually saying is that when the State struct that wraps `blurAmount` changes, print out the new blur amount.

### Creating custom bindings in SwiftUI

If we really want to observe the value change, we need to create custom bindings, we need to use the `Binding` struct directly, which allows us to provide our own code to run when the value is read or written.

We need to create a custom `Binding` struct that acts as a passthrough around `blurAmount`, but when we’re setting the value we also want to print a message.

As a result, we need to put this code into the `body` property of our view, like this:

```swift
struct ContentView: View {
    @State private var blurAmount: CGFloat = 0

    var body: some View {
        let blur = Binding<CGFloat>(
            get: {
                self.blurAmount
            },
            set: {
                self.blurAmount = $0
                print("New value is \(self.blurAmount)")
            }
        )

        return VStack {
            Text("Hello, World!")
                .blur(radius: blurAmount)

            Slider(value: blur, in: 0...20)
        }
    }
}
```

One thing that changed is the way we declare the binding in the slider: rather than using $`blurAmount` we can just use `blur`. This is because using the dollar sign is what gets us the two-way binding from some state, but now that we’ve created the binding directly we no longer need it.

OK, now let’s look at the binding itself. As you should be able to figure out from the way we used it, the basic initializer for a `Binding` looks like this:

```swift
init(get: @escaping () -> Value, set: @escaping (Value) -> Void)
```

It’s telling us that the initializer takes two closures: a getter that takes no parameters and returns a value, and a setter that takes a value and returns nothing. **`Binding`** uses generics, so that **`Value`** is really a placeholder for whatever we’re storing inside – a **`CGFloat`** in the case of our **`blur`** binding. Both the **`get`** and **`set`** closures are marked as **`@escaping`**, meaning that the **`Binding`** struct stores them for use later on.

What all this means is that you can do whatever you want inside these closures: you can call methods, run an algorithm to figure out the correct value to use, or even just use random values – it doesn’t matter, as long as you return a value from **`get`**. So, if you want to make sure you update **`UserDefaults`** every time a value is changed, the **`set`** closure of a **`Binding`** is perfect.

### Showing multiple options with ActionSheet

`Alert` shows pop box in the middle and `sheet` shows a full screen view.

`ActionSheet` is a partial screen sheet with buttons that slides up from the bottom of the screen

You can create action sheet as shown below, it's controlled by `showingActionSheet` binding

```swift
.actionSheet(isPresented: $showingActionSheet) {
    ActionSheet(title: Text("Change background"), message: Text("Select a new color"), buttons: [
        .default(Text("Red")) { self.backgroundColor = .red },
        .default(Text("Green")) { self.backgroundColor = .green },
        .default(Text("Blue")) { self.backgroundColor = .blue },
        .cancel()
    ])
}
```

## [Time for Core Data](https://www.hackingwithswift.com/100/swiftui/61)

Your job today is to expand your app so that it uses Core Data.

[xlzhsteven/friendFaceWithCoreData](https://github.com/xlzhsteven/friendFaceWithCoreData)

## [Milestone: Projects 10-12](https://www.hackingwithswift.com/100/swiftui/60)

- Fetch the data and parse it into **`User`** and **`Friend`** structs.
- Display a list of users with a little information about them.
- Create a detail view shown when a user is tapped, presenting more information about them.

Finished project is at 

[xlzhsteven/FriendFace](https://github.com/xlzhsteven/FriendFace)