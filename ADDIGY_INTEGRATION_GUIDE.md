# SplashBuddy - Addigy Integration Guide

This comprehensive guide walks through the process of integrating SplashBuddy with Addigy MDM. The code has been modified to remove JAMF and Munki functionality, focusing on Addigy integration.

## 1. Understanding the Architecture

SplashBuddy now has two ways to track software installations:
- Standard macOS Installer Log (`/var/log/install.log`)
- Addigy-specific monitoring (to be configured)

The `AddigyInsider` class is responsible for monitoring Addigy's installation progress by parsing log files or using other methods.

## 2. Implementation Steps

### Step 1: Identify Addigy Log Files
- Install the Addigy agent on a test Mac
- Check for log files in `/Library/Addigy/logs/` or run:
  ```bash
  sudo find /Library -name "*.log" | grep -i addigy
  ```
- Look for files that track software installation progress
- Note the exact path(s) of relevant log files

### Step 2: Analyze Log Patterns
- Install a test application through Addigy
- Monitor the log files in real-time:
  ```bash
  tail -f /path/to/addigy/log
  ```
- Identify patterns that indicate:
  - When installation starts (Example: "Installing", "Deploying")
  - When installation succeeds (Example: "Successfully installed", "Complete")
  - When installation fails (Example: "Failed", "Error")
- Document these patterns for use in the next step

### Step 3: Update Configuration Files

#### 3.1: Update Constants.swift
- Open `Constants.swift` in Xcode
- Update the `AddigyLogPath` constant with the correct path:
  ```swift
  static let AddigyLogPath = "/actual/path/to/addigy/log"
  ```

#### 3.2: Update SplashBuddy.entitlements
- Open `SplashBuddy.entitlements` in Xcode
- Ensure the log file path is included in the read-only permissions array:
  ```xml
  <string>/actual/path/to/addigy/log</string>
  ```

#### 3.3: Configure io.fti.SplashBuddy.plist
- Enable the Addigy insider by setting:
  ```xml
  <key>AddigyInsider</key>
  <true/>
  ```
- Configure application packages to match Addigy naming conventions

### Step 4: Implement Log Parsing

#### 4.1: Update AddigyInsider.swift
- Open `AddigyInsider.swift` in Xcode
- Modify the `check(line:)` method with your discovered patterns:
  ```swift
  func check(line: String) throws -> Software.SoftwareStatus? {
      // Replace these with actual Addigy log patterns
      if (line.contains("Starting installation of")) {
          return Software.SoftwareStatus.installing
      } else if (line.contains("Successfully installed")) {
          return Software.SoftwareStatus.success
      } else if (line.contains("Installation failed")) {
          return Software.SoftwareStatus.failed
      }
      return nil
  }
  ```

#### 4.2: Add the file to Xcode project
- In Xcode, right-click on the "Insiders" folder
- Select "Add Files to SplashBuddy..."
- Navigate to and select AddigyInsider.swift

### Step 5: Alternative Approaches (if needed)

If Addigy doesn't primarily use log files for tracking:

#### 5.1: File Monitoring Method
- Create a method that checks for the existence of specific files
- Update the insider to periodically check these files

#### 5.2: API Integration
- If Addigy provides an API for checking installation status, implement API calls
- Create a timer that periodically queries the status

#### 5.3: Status Database Monitoring
- If Addigy maintains a status database, create a method to query it
- Update the insider to read from this database

### Step 6: Testing

#### 6.1: Development Testing
- Build and run SplashBuddy in Xcode
- Add logging statements to `AddigyInsider.swift`:
  ```swift
  Log.write(string: "Processing line: \(line)", cat: "AddigyInsider", level: .debug)
  ```
- Install applications through Addigy
- Check logs to verify pattern matching

#### 6.2: Full Testing
- Test with various applications listed in your configuration
- Verify all states work: pending, installing, success, failed
- Test edge cases: cancellations, network issues, etc.

### Step 7: Packaging and Deployment

#### 7.1: Build for Deployment
- Archive SplashBuddy in Xcode (Product > Archive)
- Export the application

#### 7.2: Create Deployment Package
- Create a package that includes:
  - SplashBuddy.app
  - Configuration files
  - Assets (icons, HTML presentation, etc.)
- Use the existing packaging scripts in the Installer directory

#### 7.3: Deploy with Addigy
- Upload the package to Addigy
- Configure deployment policy
- Test on a pilot group before full deployment

## 3. Troubleshooting

### Common Issues
- **Log file not found**: Verify the path in Constants.swift and permissions
- **No status updates**: Check log parsing patterns in AddigyInsider.swift
- **Missing packages**: Verify packageName in applicationsArray matches Addigy names
- **Permissions issues**: Check entitlements for proper file access

### Debugging Techniques
- Enable verbose logging by modifying Log.swift
- Monitor log file changes during installation:
  ```bash
  sudo fs_usage | grep addigy
  ```
- Add temporary logging to key functions in AddigyInsider.swift

## 4. Resources

- Addigy API documentation (if available)
- macOS system logging: `log stream --predicate 'subsystem contains "addigy"'`
- SplashBuddy documentation in README.md

## 5. Future Improvements

- Consider building a direct API integration with Addigy
- Add support for Addigy-specific features or workflows
- Create custom presentation templates optimized for Addigy deployments

---

This guide provides a framework for integration. The specific log patterns and paths will depend on your Addigy environment. Adapt as needed for your specific deployment requirements.