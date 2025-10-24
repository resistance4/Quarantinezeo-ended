# Ticket Text Commands Fix

## Problem
Ticket text commands (`ticket` and `ticketclose`) were not responding when users tried to create a ticket panel or close tickets.

## Root Cause
The `messageCreate` event handler was incorrectly nested inside the `voiceStateUpdate` event handler, preventing it from being registered properly at bot startup. This caused all message-based commands (including ticket commands) to not work.

**Location of Issue:** Line 5331 in `index.js`

## Changes Made

### 1. Fixed Event Handler Structure (index.js)
**Before:**
```javascript
client.on('voiceStateUpdate', async (oldState, newState) => {
    if (musicManager) {
        musicManager.handleVoiceUpdate(oldState, newState);
    }

    // Handle voice management
    voiceManager.handleVoiceStateUpdate(oldState, newState);

    // Handle message commands
client.on('messageCreate', async message => {
```

**After:**
```javascript
client.on('voiceStateUpdate', async (oldState, newState) => {
    if (musicManager) {
        musicManager.handleVoiceUpdate(oldState, newState);
    }

    // Handle voice management
    voiceManager.handleVoiceStateUpdate(oldState, newState);
});

// Handle message commands
client.on('messageCreate', async message => {
```

**Change:** Added closing `});` to properly close the `voiceStateUpdate` handler before starting the `messageCreate` handler.

### 2. Removed Duplicate Closing Brace (index.js)
**Location:** Line 5810

**Before:**
```javascript
    if (mediaThreadsManager) {
        await mediaThreadsManager.handleMessage(message);
    }
});

// Close message create event handler properly
});

// === SECURITY MANAGER EVENT LISTENERS ===
```

**After:**
```javascript
    if (mediaThreadsManager) {
        await mediaThreadsManager.handleMessage(message);
    }
});

// === SECURITY MANAGER EVENT LISTENERS ===
```

**Change:** Removed duplicate closing brace that was causing syntax errors.

### 3. Added Ticket Commands to Authorization List (index.js)
**Location:** Lines 8154-8179

**Change:** Added `'ticket'` and `'ticketclose'` to the `commandLikePatterns` array to ensure they're properly recognized as commands in the authorization system.

```javascript
// Ticket Commands
'ticket', 'ticketclose'
```

## Testing

### Syntax Validation
✅ No syntax errors: `node -c index.js` passes

### Commands Ready for Testing
- ✅ `ticket <channel_id> [@role]` - Create ticket panel (no prefix)
- ✅ `!ticket <channel_id> [@role]` - Create ticket panel (with prefix)
- ✅ `ticketclose` - Close current ticket (no prefix)
- ✅ `!ticketclose` - Close current ticket (with prefix)
- ✅ `!ticketclose #channel` - Close specific ticket

## Expected Behavior

### Creating a Ticket Panel
```
Command: ticket #support @Support Team
Result: Bot creates a ticket panel in #support channel with a "📩 Open Ticket" button
```

### Closing a Ticket
```
Command: ticketclose
Result: Bot closes the current ticket channel after 5-second countdown
```

## Files Modified
1. `/workspace/index.js`
   - Line 5329: Added proper closing for voiceStateUpdate handler
   - Line 5810: Removed duplicate closing brace
   - Line 8177: Added ticket commands to authorization list

## Summary
The ticket text commands are now properly registered and should respond when users type:
- `ticket <channel_id> [@role]` or `!ticket <channel_id> [@role]` to create panels
- `ticketclose` or `!ticketclose` to close tickets

The fix ensures the messageCreate event handler is registered at the correct level, allowing all text commands to function properly.
