# SplashBuddy - Addigy Implementation Guide

This document outlines how to configure SplashBuddy to work with Addigy MDM. This version of SplashBuddy has been streamlined to work primarily with Addigy, with JAMF and Munki functionality removed.

## Available Installation Tracking Methods

SplashBuddy now has two ways to track software installations:

1. **Standard macOS Installer Log** - Monitors `/var/log/install.log` (enabled by default)
2. **Addigy Specific Tracking** - Monitors Addigy's logs (must be configured)

## Log File Location

To properly track software installation progress with Addigy, you need to identify where Addigy logs installation activities. The default path in this implementation is:

```
/Library/Addigy/logs/addigy_agent.log
```

If this is incorrect, update the following locations:

1. `Constants.swift` - Update the `AddigyLogPath` constant
2. `SplashBuddy.entitlements` - Update the path in the read-only permissions array

## Addigy Log Format

The `AddigyInsider.swift` file contains placeholders for the log parsing logic. You need to:

1. Examine Addigy's log files to identify patterns that indicate:
   - When an installation begins
   - When an installation completes successfully
   - When an installation fails

2. Update the `check(line:)` method in `AddigyLineChecker` with these patterns

## Enabling the Addigy Insider

To enable the Addigy insider, add the following to your `io.fti.SplashBuddy.plist` configuration:

```xml
<key>AddigyInsider</key>
<true/>
```

## Alternative Implementation

If Addigy doesn't use log files for tracking installation progress, you may need to:

1. Extend the `InsiderProtocol` or create a new tracking method
2. Implement a custom status tracking mechanism
3. Update the `run()` method to check installation status in a different way

## Package Naming

Make sure the `packageName` values in your `applicationsArray` match the names of packages as they appear in Addigy logs. This is essential for proper tracking.

## Testing Your Implementation

To verify your Addigy integration:

1. Run SplashBuddy with debug logging enabled
2. Install a test application through Addigy
3. Check that SplashBuddy correctly shows the installation status
4. Verify that all states (pending, installing, success, failed) work correctly

## Troubleshooting

If SplashBuddy is not tracking Addigy installations correctly:

1. Verify the log path is correct
2. Check that the log parsing patterns match Addigy's log format
3. Ensure the Addigy insider is enabled in the plist configuration
4. Verify entitlements are set correctly for file access
5. Confirm package names match what appears in logs

For additional assistance, refer to the main SplashBuddy documentation.