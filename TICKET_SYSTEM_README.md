# 🎫 Ticket System Documentation

## Overview
This is a comprehensive ticket system for Discord that allows users to create support tickets with a button-based interface. When a ticket is created, a private channel is automatically generated where only the ticket creator, admins, and server owner can view and participate.

## Features
✅ Button-based ticket creation
✅ Automatic private category creation
✅ Permission management (creator, admins, server owner only)
✅ Role ping notifications
✅ Easy ticket closing with commands or buttons
✅ Automatic cleanup on channel deletion
✅ Ticket numbering system

## Setup Commands

### 1. Create Ticket Panel
**Command:** `ticket <channel_id> [@role]` (no prefix needed)

**Description:** Sets up a ticket panel in the specified channel with a button that users can click to open tickets.

**Parameters:**
- `channel_id` - The ID or mention of the channel where the ticket panel will be posted
- `@role` (optional) - Role to ping when a ticket is created

**Examples:**
```
ticket 1234567890
ticket #support
ticket 1234567890 @Support Team
ticket #support @Staff
```

**What happens:**
1. Bot creates an embed with a "📩 Open Ticket" button in the specified channel
2. The panel message explains how to use the ticket system
3. Role (if specified) will be pinged when tickets are created

**Required Permissions:**
- User: Manage Channels permission, Server Owner, or Bot Owner
- Bot: Manage Channels, Manage Roles

---

### 2. Close Tickets
**Command:** `ticketclose [channel_id]` (no prefix needed)

**Description:** Closes a ticket channel.

**Parameters:**
- `channel_id` (optional) - The ID or mention of the ticket channel to close. If not provided, closes the current channel.

**Examples:**
```
ticketclose
ticketclose 1234567890
ticketclose #ticket-5
```

**What happens:**
1. Verifies the channel is a valid ticket channel
2. Sends a closing message
3. Deletes the ticket channel after 5 seconds

**Required Permissions:**
- User: Manage Channels permission, Administrator permission, or Server Owner
- Bot: Manage Channels

---

## How Users Create Tickets

### Step-by-Step Process:

1. **User clicks the "📩 Open Ticket" button** in the ticket panel channel
   
2. **Bot checks for existing tickets**
   - If user already has an active ticket, they get an error message
   - If not, the process continues

3. **Bot creates/finds "Tickets" category**
   - If a category named "Tickets" exists, it uses that
   - If not, it creates a new private category named "Tickets"
   - Category is hidden from @everyone

4. **Bot creates ticket channel**
   - Channel name: `ticket-{number}` (e.g., `ticket-1`, `ticket-2`)
   - Permissions set for:
     - ❌ @everyone: Cannot view channel
     - ✅ Ticket Creator: Can view, send messages, attach files
     - ✅ Bot: Full permissions
     - ✅ Server Owner: Full permissions
     - ✅ Administrator Role: Full permissions

5. **Bot sends welcome message**
   - Includes ticket number
   - Tags the user and support role (if configured)
   - Shows when the ticket was created
   - Includes a "🔒 Close Ticket" button

6. **User describes their issue**
   - Staff members can now help the user
   - Conversation stays private

7. **Ticket is closed**
   - Staff member clicks "🔒 Close Ticket" button, OR
   - Staff member uses `!ticketclose` command
   - Channel is deleted after 5 seconds

---

## Channel Permissions Breakdown

### Tickets Category
- **@everyone:** ❌ Cannot view
- Purpose: Keeps all tickets hidden from regular members

### Individual Ticket Channels
- **@everyone:** ❌ Cannot view
- **Ticket Creator:** ✅ View, Send Messages, Read History, Attach Files, Embed Links
- **Server Owner:** ✅ Full access
- **Administrator Role:** ✅ Full access (if exists)
- **Support Role (if configured):** ✅ Pinged but inherits admin permissions
- **Bot:** ✅ Full access

---

## Button Interactions

### "📩 Open Ticket" Button
- **ID:** `open_ticket`
- **Style:** Primary (Blue)
- **Function:** Creates a new ticket channel
- **Response:** Ephemeral (only visible to user)

### "🔒 Close Ticket" Button
- **ID:** `close_ticket`
- **Style:** Danger (Red)
- **Function:** Closes the ticket channel
- **Permission:** Requires Manage Channels, Administrator, or Server Owner
- **Response:** Public (visible to all in ticket)

---

## Automatic Features

### 1. One Ticket Per User
- Users can only have one active ticket at a time
- If they try to create another, they get redirected to their existing ticket

### 2. Ticket Numbering
- Each server has its own ticket counter
- Numbers increment: ticket-1, ticket-2, ticket-3, etc.
- Numbers are tracked per server (guild)

### 3. Auto-Cleanup
- If a ticket channel is deleted manually, the system automatically removes it from tracking
- No orphaned data left behind

### 4. Category Management
- Bot automatically creates a "Tickets" category if it doesn't exist
- Category is private (hidden from @everyone)
- All tickets are organized under this category

---

## Example Workflow

### Setting Up
```
1. Admin runs: ticket #support @Support Team
2. Bot posts ticket panel in #support
3. Users can now click the button to create tickets
```

### User Opens Ticket
```
1. User clicks "📩 Open Ticket"
2. Bot creates #ticket-1 (hidden channel)
3. User receives confirmation: "✅ Your ticket has been created: #ticket-1"
4. In #ticket-1:
   - User is tagged
   - @Support Team is pinged
   - Welcome message appears
   - Close button is available
```

### Staff Helps User
```
1. Support staff sees the ping in #ticket-1
2. They view the channel and help the user
3. Conversation happens privately
4. Issue is resolved
```

### Closing the Ticket
```
Option 1: Button
1. Staff clicks "🔒 Close Ticket"
2. Closing message appears
3. Channel deletes after 5 seconds

Option 2: Command
1. Staff types: ticketclose
2. Same result as button method

Option 3: Command from another channel
1. Staff types: ticketclose #ticket-1
2. Same result
```

---

## Troubleshooting

### "I need Manage Channels permission"
- Ensure the bot has the "Manage Channels" permission in server settings
- Ensure the bot has "Manage Roles" permission

### "Invalid channel ID provided"
- Make sure you're using a valid channel ID or mention
- Channel must exist in the same server

### "This is not a valid ticket channel"
- The channel you're trying to close isn't a ticket
- Only channels created by the ticket system can be closed this way

### "You already have an active ticket"
- Close your existing ticket before creating a new one
- Find your open ticket and use it instead

### Tickets category not being created
- Check bot permissions for "Manage Channels"
- Make sure bot isn't rate limited

---

## Technical Details

### Files Modified
1. **ticketManagement.js** - New file containing ticket system logic
2. **database.js** - Updated with ticket storage functions
3. **index.js** - Integrated ticket commands and button handlers

### Storage
- Ticket panels: Stored in-memory (Map)
- Active tickets: Stored in-memory (Map)
- Ticket numbers: Stored in-memory per guild (Map)

### Event Handlers
- `messageCreate` - Handles !ticket and !ticketclose commands
- `interactionCreate` - Handles button clicks (open_ticket, close_ticket)
- `channelDelete` - Cleans up deleted ticket channels

---

## Tips for Server Admins

1. **Choose the right channel** for the ticket panel
   - Use a dedicated #support or #help channel
   - Pin the ticket panel message for easy access

2. **Set up a support role**
   - Create a @Support Team role
   - Give it to your support staff
   - Use it in the !ticket command to ping them

3. **Monitor ticket activity**
   - Check the Tickets category regularly
   - Ensure tickets are being closed when resolved

4. **Customize the panel location**
   - You can create multiple ticket panels in different channels
   - Each panel can ping different roles (e.g., @Tech Support, @Billing Support)

5. **Train your staff**
   - Show them how to use !ticketclose
   - Remind them about the close button
   - Explain the private nature of tickets

---

## Command Summary

| Command | Usage | Permission Required |
|---------|-------|-------------------|
| `ticket` | `ticket <channel_id> [@role]` | Manage Channels, Server Owner, or Bot Owner |
| `ticketclose` | `ticketclose [channel_id]` | Manage Channels, Administrator, or Server Owner |

---

## Button Summary

| Button | Location | Function | Permission Required |
|--------|----------|----------|-------------------|
| 📩 Open Ticket | Ticket Panel | Creates new ticket | None (any user) |
| 🔒 Close Ticket | Inside Ticket | Closes ticket | Manage Channels, Administrator, or Server Owner |

---

## Support

If you encounter any issues with the ticket system, please check:
1. Bot permissions (Manage Channels, Manage Roles)
2. Command syntax
3. Your user permissions
4. Bot logs for error messages

---

**Version:** 1.0.0  
**Last Updated:** 2025-10-24
