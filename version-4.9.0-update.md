# Version 4.9.0 Update

We're excited to announce the release of TeleBot Creator 4.9.0 with significant improvements to stability, performance, and functionality.

## Major Improvements

### 🛠️ Bug Fixes
- Most of the previously reported bugs have been fixed in this release
- Improved stability across all components and libraries

### ⏱️ New Timing Controls
- Added native `time.sleep()` function with a maximum limit of 10 seconds
- Increased code execution timeout from 60 to 120 seconds
- Enhanced `run_after` command:
  - Maximum timeout extended to 1 year (365 days)
  - Minimum timeout reduced to 0.1 seconds
  - Smart rate limiting: For ultra-fast commands (under 0.4 seconds), a limit of 5 executions within 5 seconds to prevent abuse
  - Increased maximum scheduled `run_after` commands per user from 20 to 100

### 💰 TON Integration
- Added comprehensive TON blockchain support through the new `TonLib` (see dedicated [TON Library Documentation](ton-library-documentation.md))
- Features include wallet creation, balance checking, transactions, and more

### 🔄 Code Improvements
- Direct HTTP module usage is now recommended over `libs.customHTTP()`
- For handling inline queries and other update types, use the `/handler_<update_type>` command format

### ⚠️ Important Changes
- The `import x` statement should not be present in any user code
- Use built-in libraries and modules instead of external imports

## Documentation Updates
We've added new documentation pages:
- [TON Library Documentation](ton-library-documentation.md): Complete guide to blockchain integration
- Updated examples and use cases across all documentation

## Upgrading
To take advantage of these new features, simply restart your bot or create a new one. All improvements are automatically available in your workspace.

## Feedback
As always, we value your feedback. If you encounter any issues or have suggestions for further improvements, please let us know through our support channels. 