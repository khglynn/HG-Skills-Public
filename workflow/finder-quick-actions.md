# Finder Quick Actions (macOS Services)

*Created: 2025-01-25*

How to create custom right-click menu items for Finder using Automator Quick Actions.

---

## Overview

Quick Actions are Automator workflows that appear in Finder's right-click > Services menu. They can run AppleScript, shell scripts, or Automator actions on selected files/folders.

**Location:** `~/Library/Services/*.workflow`

**Example:** "md now" - creates a new markdown file in the selected folder and opens it.

---

## Creating a Quick Action

### Method 1: Automator (Recommended)

1. Open Automator (Cmd+Space > "Automator")
2. Choose "Quick Action" as document type
3. Configure the top bar:
   - **Workflow receives:** `files or folders`
   - **in:** `Finder`
4. Add actions (drag from left sidebar)
5. Save (Cmd+S) - saves to `~/Library/Services/`

### Method 2: Programmatic (for Claude)

Create the workflow bundle structure:

```bash
mkdir -p ~/Library/Services/"My Action.workflow"/Contents
```

**Contents/Info.plist:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>NSServices</key>
    <array>
        <dict>
            <key>NSMenuItem</key>
            <dict>
                <key>default</key>
                <string>My Action</string>
            </dict>
            <key>NSMessage</key>
            <string>runWorkflowAsService</string>
            <key>NSRequiredContext</key>
            <dict>
                <key>NSApplicationIdentifier</key>
                <string>com.apple.finder</string>
            </dict>
            <key>NSSendFileTypes</key>
            <array>
                <string>public.item</string>
            </array>
        </dict>
    </array>
</dict>
</plist>
```

**Contents/document.wflow:** Complex plist - easier to create via Automator then modify.

### Best Practice: Hybrid Approach

1. Create basic workflow in Automator with correct settings
2. Save it
3. Modify the AppleScript/shell script programmatically if needed:

```bash
open ~/Library/Services/"My Action.workflow"
# Make changes in Automator, Cmd+S to save
```

---

## Key Settings for Finder Context Menu

For a Quick Action to appear in Finder's right-click menu:

| Setting | Value | Why |
|---------|-------|-----|
| Workflow receives | `files or folders` | Not "no input" |
| in | `Finder` | Not "any application" |
| serviceInputTypeIdentifier | `com.apple.Automator.fileSystemObject` | In document.wflow |

**Wrong settings = appears in "General" category in System Settings, not "Files and Folders", and won't show in Finder context menu.**

---

## Refreshing Services

After creating/modifying a workflow:

```bash
/System/Library/CoreServices/pbs -update && killall Finder
```

If category doesn't update, open in Automator and re-save. Logout/login may be needed for stubborn cases.

---

## Example: Create Markdown File

AppleScript that creates `Untitled.md` in the selected folder:

```applescript
on run {input, parameters}
    if input is not {} then
        set targetItem to item 1 of input
        tell application "System Events"
            set itemPath to POSIX path of targetItem
            if (class of (disk item itemPath)) is folder then
                set folderPath to itemPath
            else
                set folderPath to POSIX path of (container of (disk item itemPath))
            end if
        end tell
    else
        tell application "Finder"
            set folderPath to POSIX path of (insertion location as alias)
        end tell
    end if

    if folderPath does not end with "/" then set folderPath to folderPath & "/"

    -- Find unique filename
    set baseName to "Untitled"
    set ext to ".md"
    set counter to 0
    set newFileName to baseName & ext

    repeat
        set fullPath to folderPath & newFileName
        tell application "System Events"
            if not (exists file fullPath) then exit repeat
        end tell
        set counter to counter + 1
        set newFileName to baseName & " " & counter & ext
    end repeat

    do shell script "touch " & quoted form of (folderPath & newFileName)

    tell application "Finder"
        open (POSIX file (folderPath & newFileName) as alias)
    end tell

    return input
end run
```

---

## Managing Services

**List installed:** `ls ~/Library/Services/`

**Enable/disable:** System Settings > Keyboard > Keyboard Shortcuts > Services

**Add keyboard shortcut:** Same location, double-click "none" next to service name

**Edit:** `open ~/Library/Services/"Service Name.workflow"`

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Service not in Finder menu | Check "Workflow receives" = files or folders, "in" = Finder |
| Service in wrong category | Re-save from Automator, or logout/login |
| Service doesn't run | Check script syntax in Automator, look at Console.app for errors |
| Changes not appearing | Run `pbs -update && killall Finder` |

---

## Kevin's Quick Actions

| Name | Purpose | Location |
|------|---------|----------|
| md now | Create new .md file in folder | `~/Library/Services/md now.workflow` |
| Convert to evil mp4 | Video conversion | `~/Library/Services/Convert to evil mp4.workflow` |
