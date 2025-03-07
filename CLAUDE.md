# SplashBuddy Development Guide

## Build Commands
- Build: `xcodebuild -scheme SplashBuddy -project SplashBuddy.xcodeproj build`
- Test all: `xcodebuild test -scheme SplashBuddy -project SplashBuddy.xcodeproj`
- Test single: `xcodebuild test -scheme SplashBuddy -project SplashBuddy.xcodeproj -only-testing:SplashBuddyTests/PreferencesTests/testSetupDoneSet`

## Code Style
- **Imports**: Foundation/AppKit first, then alphabetical packages
- **Naming**: PascalCase for types, camelCase for variables/functions
- **Documentation**: Class and function descriptions with parameter docs
- **Organization**: Use MARK comments to organize code sections
- **Error Handling**: Custom error enums with specific cases, use `do-catch` and `guard`
- **Logging**: Use `Log` class with appropriate severity levels
- **Testing**: Each feature should have corresponding tests

## Architecture
- MVC pattern with clear separation of business logic from UI
- Singletons accessed via `sharedInstance` pattern
- Use Swift's type system with explicit optionals and strong typing
- Thread-safe operations using GCD for background tasks