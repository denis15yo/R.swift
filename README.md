# R.swift [![Version](https://img.shields.io/cocoapods/v/R.swift.svg?style=flat)](https://cocoapods.org/pods/R.swift) [![License](https://img.shields.io/cocoapods/l/R.swift.svg?style=flat)](License) ![Platform](https://img.shields.io/cocoapods/p/R.swift.svg?style=flat)

_Get strong typed, autocompleted resources like images, fonts and segues in Swift projects_

## Why use this?

It makes your code that uses resources:
- **Fully typed**, less casting and guessing what a method will return
- **Compile time checked**, no more incorrect strings that make your app crash at runtime
- **Autocompleted**, never have to guess that image name again

Currently you type:
```swift
let icon = UIImage(named: "settings-icon")
let font = UIFont(name: "San Francisco", size: 42)
let color = UIColor(named: "indicator highlight")
let viewController = CustomViewController(nibName: "CustomView", bundle: nil)
let string = String(format: NSLocalizedString("welcome.withName", comment: ""), locale: NSLocale.current, "Arthur Dent")
```

With R.swift it becomes:
```swift
let icon = R.image.settingsIcon()
let font = R.font.sanFrancisco(size: 42)
let color = R.color.indicatorHighlight()
let viewController = CustomViewController(nib: R.nib.customView)
let string = R.string.localizable.welcomeWithName("Arthur Dent")
```

Check out [more examples](Documentation/Examples.md) or hear about [how Fabric.app uses R.swift](https://academy.realm.io/posts/slug-javi-soto-building-fabric-in-swift/#rswift-2956)!

## Demo

**Autocompleted images:**

![Autocompleted images](Documentation/Images/DemoUseImage.gif)

**Compiletime checked images:**

![Compiletime checked images](Documentation/Images/DemoRenameImage.gif)

This is only the beginning, check out [more examples](Documentation/Examples.md)!

## CocoaHeadsNL presentation

Mathijs Kadijk presented R.swift at the September 2016 CocoaHeadsNL meetup.
Talking about the ideas behind R.swift and demonstrating how to move from plain stringly-typed iOS code to statically typed code.

<a href="https://www.youtube.com/embed/C8kRUTV9TOA"><img src="https://i.ytimg.com/vi/C8kRUTV9TOA/maxresdefault.jpg" width="560" alt="R.swift presentation at CocoaHeadsNL"></a>

## Features

After installing R.swift into your project you can use the `R`-struct to access resources. If the struct is outdated just build and R.swift will correct any missing/changed/added resources.

R.swift currently supports these types of resources:
- [Images](Documentation/Examples.md#images)
- [Fonts](Documentation/Examples.md#custom-fonts)
- [Resource files](Documentation/Examples.md#resource-files)
- [Colors](Documentation/Examples.md#colors)
- [Localized strings](Documentation/Examples.md#localized-strings)
- [Storyboards](Documentation/Examples.md#storyboards)
- [Segues](Documentation/Examples.md#segues)
- [Nibs](Documentation/Examples.md#nibs)
- [Reusable cells](Documentation/Examples.md#reusable-table-view-cells)
- [Project](Documentation/Examples.md#project)
- [Entitlements](Documentation/Examples.md#entitlements)
- [Info.plist](Documentation/Examples.md#info-plist)

Runtime validation with [`R.validate()`](Documentation/Examples.md#runtime-validation):
- If all images used in storyboards and nibs are available
- If all named colors used in storyboards and nibs are available
- If all view controllers with storyboard identifiers can be loaded
- If all custom fonts can be loaded

## Q&A

- [Why was R.swift created?](Documentation/QandA.md#why-was-rswift-created)
- [Why should I choose R.swift over alternative X or Y?](Documentation/QandA.md#why-should-i-choose-rswift-over-alternative-x-or-y)
- [What are the requirements to run R.swift?](Documentation/QandA.md#what-are-the-requirements-to-run-rswift)
- [How to fix missing imports in the generated file?](Documentation/QandA.md#how-to-fix-missing-imports-in-the-generated-file)
- [How to use classes with the same name as their module?](Documentation/QandA.md#how-to-use-classes-with-the-same-name-as-their-module)
- [Can I ignore resources?](Documentation/Ignoring.md)
- [Can I use R.swift in a library?](Documentation/QandA.md#can-i-use-rswift-in-a-library)
- [How does R.swift work?](Documentation/QandA.md#how-does-rswift-work)
- [How to upgrade to a new major version?](Documentation/Migration.md)
- [How can I only run specific generators?](Documentation/Ignoring.md#only-run-specific-generators-exclude-rsomething)

## Installation

As of Rswift 7, Swift Package Manager is the recommended method of installation.

[Demo video: Updating from R.swift 6 to Rswift 7](https://youtu.be/icihJ_hin3I?t=66) (Starting at 1:06, this describes the installation of Rswift 7).

### Xcode project using SPM (Recommended)

[Demo Video: Install R.swift in Xcode with SPM](Documentation/RswiftSPMInstallation.mp4)

1. In Project Settings, on the tab "Package Dependencies", click "+", search for `https://github.com/mac-cain13/R.swift` and click "Add Package".
2. Select the target that will use R.swift next to "RswiftLibrary" and click "Add Package".
4. Now click on your target, on the tab "Build Phases", in the section "Run Build Tool Plug-ins", click "+" and add `RswiftGenerateInternalResources`. ([Screenshot](Documentation/Images/RunBuildToolPluginsRswift.png))
5. Now the `R` struct should be available in your code, use auto-complete to explore all static references.

Note: The first build you might need to approve the new plugin by clicking the build error warning you about the new plugin.

#### R.swift on Xcode Cloud or any other CI

On your CI server you can't explicitly allow the build plugin to run, so you need to disable plugin validation to be able to build without user interaction:

5. Run a script on your CI that runs: `defaults write com.apple.dt.Xcode IDESkipPackagePluginFingerprintValidatation -bool YES` before Xcode starts building.

On Xcode Cloud you can add a [custom build script](https://developer.apple.com/documentation/xcode/writing-custom-build-scripts) in `ci_scripts/ci_post_clone.sh` with this line that Xcode will run.

### Package.swift based SPM project

1. Add a dependency in Package.swift:
    ```swift
    dependencies: [
        .package(url: "https://github.com/mac-cain13/R.swift.git", from: "7.0.0")
    ]
    ```
2. For each relevant target, add a dependency and a plugin
    ```swift
    .target(
        name: "Example",
        dependencies: [.product(name: "RswiftLibrary", package: "R.swift")],
        plugins: [.plugin(name: "RswiftGeneratePublicResources", package: "R.swift")]
    )
    ```
3. Build your project, now the `R` struct should be available in your code, use auto-complete to explore all static references

<details>
<summary><h3>CocoaPods</h3></summary>

1. Add `pod 'R.swift'` to your [Podfile](http://cocoapods.org/#get_started) and run `pod install`
2. In Xcode: Click on your project in the file list, choose your target under `TARGETS`, click the `Build Phases` tab and add a `New Run Script Phase` by clicking the little plus icon in the top left
3. Drag the new `Run Script` phase **above** the `Compile Sources` phase and **below** `Check Pods Manifest.lock`, expand it and paste the following script:
   ```bash
   "$PODS_ROOT/R.swift/rswift" generate "$SRCROOT/R.generated.swift"
   ```
4. Add `$SRCROOT/R.generated.swift` to the "Output Files" of the Build Phase
5. Uncheck "Based on dependency analysis" so that R.swift is run on each build
6. Build your project, in Finder you will now see a `R.generated.swift` in the `$SRCROOT`-folder, drag the `R.generated.swift` files into your project and **uncheck** `Copy items if needed`

_Screenshot of the Build Phase can be found [here](Documentation/Images/BuildPhaseExample.png)_

_Tip:_ Add the `*.generated.swift` pattern to your `.gitignore` file to prevent unnecessary conflicts.
</details>

<details>
<summary><h3>Manually</h3></summary>

0. Add the [R.swift](https://github.com/mac-cain13/R.swift) library to your project
1. [Download](https://github.com/mac-cain13/R.swift/releases) a R.swift release, unzip it and put it into your source root directory
2. In Xcode: Click on your project in the file list, choose your target under `TARGETS`, click the `Build Phases` tab and add a `New Run Script Phase` by clicking the little plus icon in the top left
3. Drag the new `Run Script` phase **above** the `Compile Sources` phase, expand it and paste the following script:  
   ```bash
   "$SRCROOT/rswift" generate "$SRCROOT/R.generated.swift"
   ```
4. Add `$SRCROOT/R.generated.swift` to the "Output Files" of the Build Phase
5. Uncheck "Based on dependency analysis" so that R.swift is run on each build
6. Build your project, in Finder you will now see a `R.generated.swift` in the `$SRCROOT`-folder, drag the `R.generated.swift` files into your project and **uncheck** `Copy items if needed`

_Screenshot of the Build Phase can be found [here](Documentation/Images/ManualBuildPhaseExample.png)_

_Tip:_ Add the `*.generated.swift` pattern to your `.gitignore` file to prevent unnecessary conflicts.
</details>

## Contribute

We'll love contributions, read the [contribute docs](Documentation/Contribute.md) for info on how to report issues, submit ideas and submit pull requests!

## License

[R.swift](https://github.com/mac-cain13/R.swift) is created by [Mathijs Kadijk](https://github.com/mac-cain13) and [Tom Lokhorst](https://github.com/tomlokhorst) released under a [MIT License](License).
