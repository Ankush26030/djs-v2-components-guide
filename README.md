---
description: Discord.js V2 Components — All bot messages MUST use Components V2 (ContainerBuilder) instead of plain text or classic embeds
---

# Discord.js V2 Components Standard

## 🚨 CRITICAL RULE
**Every single bot message in this project MUST use Discord.js Components V2 (ContainerBuilder).**
This applies to ALL message types — replies, sends, DMs, followUps, editReplys, log messages, everything.

- ❌ NEVER use plain text replies like `message.reply('some text')`
- ❌ NEVER use `{ content: 'some text' }`
- ❌ NEVER use classic `EmbedBuilder` / `{ embeds: [...] }`
- ❌ NEVER use `ephemeral: true` (old style)
- ❌ NEVER use `.send('plain text')` or `.send({ content: '...' })`
- ✅ ALWAYS use `ContainerBuilder` + `TextDisplayBuilder` with `MessageFlags.IsComponentsV2`
- ✅ ALWAYS include `allowedMentions: { parse: [] }` in every message
- ✅ ALWAYS set `setAccentColor()` using config colors based on message type

---

## Required Imports

```js
const {
    ContainerBuilder,
    TextDisplayBuilder,
    SeparatorBuilder,
    SectionBuilder,
    ThumbnailBuilder,
    MediaGalleryBuilder,
    MediaGalleryItemBuilder,
    MessageFlags,
} = require('discord.js');
```

> **Note:** Only import the builders you actually use in each file.

---

## Color Config (from config.js)

Every project using V2 components should have a `config.js` with these colors:

```js
// config.js
module.exports = {
    colors: {
        primary: 0x5865F2,   // Discord Blurple — for info, general messages
        success: 0x57F287,   // Green — for success confirmations
        error: 0xED4245,     // Red — for errors, permission denied, failures
        warning: 0xFEE75C,   // Yellow — for warnings, already exists, caution
        info: 0x5865F2,      // Blue — for informational messages
        profile: 0xEB459E,   // Pink — for profile-related
        modmail: 0xFF9B50,   // Orange — for modmail-related
    },
};
```

Usage in command files:
```js
const { colors } = require('../../config'); // adjust path as needed
```

---

## ✅ Standard Patterns

### 1. Error Messages
```js
const errContainer = new ContainerBuilder()
    .setAccentColor(colors.error)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('## ❌ Error Title')
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('Error description goes here.')
    );
await ctx.reply({
    components: [errContainer],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 2. Success Messages
```js
const successContainer = new ContainerBuilder()
    .setAccentColor(colors.success)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('## ✅ Success Title')
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('Success description goes here.')
    );
await ctx.reply({
    components: [successContainer],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 3. Warning Messages
```js
const warnContainer = new ContainerBuilder()
    .setAccentColor(colors.warning)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('## ⚠️ Warning Title')
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('Warning description goes here.')
    );
await ctx.reply({
    components: [warnContainer],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 4. Info/Primary Messages (with separator and footer)
```js
const container = new ContainerBuilder()
    .setAccentColor(colors.primary)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('## 📋 Title')
    )
    .addSeparatorComponents(new SeparatorBuilder().setDivider(true))
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('Main content here with **bold** and `code` formatting.')
    )
    .addSeparatorComponents(new SeparatorBuilder().setDivider(true))
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('-# Footer text here')
    );
await ctx.reply({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 5. Message with Thumbnail (Section)
```js
const container = new ContainerBuilder()
    .setAccentColor(colors.primary)
    .addSectionComponents(
        new SectionBuilder()
            .addTextDisplayComponents(
                new TextDisplayBuilder().setContent('## Title\nDescription text here')
            )
            .setThumbnailAccessory(
                new ThumbnailBuilder().setURL(user.displayAvatarURL({ extension: 'png', size: 256 }))
            )
    );
await ctx.reply({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 6. Ephemeral Messages (Slash Commands only)
```js
// Combine MessageFlags.Ephemeral with IsComponentsV2 using bitwise OR
await interaction.reply({
    components: [container],
    flags: MessageFlags.IsComponentsV2 | MessageFlags.Ephemeral,
    allowedMentions: { parse: [] },
});
```

### 7. Permission Denied (Common Pattern)
```js
const noPermContainer = new ContainerBuilder()
    .setAccentColor(colors.error)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('## ❌ No Permission')
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('You do not have permission to use this command.')
    );
return message.reply({
    components: [noPermContainer],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 8. Usage/Help Messages
```js
const usageContainer = new ContainerBuilder()
    .setAccentColor(colors.error)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('## ❌ Usage')
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('`!command <required> [optional]`')
    );
return message.reply({
    components: [usageContainer],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 9. Channel Send (Logs, Notifications, etc.)
```js
// Sending to a channel (not replying)
const logContainer = new ContainerBuilder()
    .setAccentColor(colors.info)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('## 📋 Log Title')
    )
    .addSeparatorComponents(new SeparatorBuilder().setDivider(true))
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('Log details here.')
    );
await channel.send({
    components: [logContainer],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 10. DM Messages (user.send)
```js
// Sending DMs to users
const dmContainer = new ContainerBuilder()
    .setAccentColor(colors.primary)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('## 📩 DM Title')
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('DM content here.')
    );
await user.send({
    components: [dmContainer],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 11. Edit Reply (Deferred Interactions)
```js
// When using deferReply() first, then editReply()
await interaction.deferReply();
// ... do async work ...
const container = new ContainerBuilder()
    .setAccentColor(colors.success)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('## ✅ Done')
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('Task completed successfully.')
    );
await interaction.editReply({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 12. Follow Up Messages
```js
// When interaction already replied, use followUp
await interaction.followUp({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 13. Image/Media Gallery
```js
const container = new ContainerBuilder()
    .setAccentColor(colors.primary)
    .addMediaGalleryComponents(
        new MediaGalleryBuilder()
            .addItems(
                new MediaGalleryItemBuilder().setURL('attachment://image.png')
            )
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('-# Footer text')
    );
await ctx.reply({
    components: [container],
    files: [attachment],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { parse: [] }
});
```

### 14. Complex Multi-Section Message
```js
const container = new ContainerBuilder()
    .setAccentColor(colors.primary)
    .addSectionComponents(
        new SectionBuilder()
            .addTextDisplayComponents(
                new TextDisplayBuilder().setContent(`## Title\nSubtitle or description`)
            )
            .setThumbnailAccessory(
                new ThumbnailBuilder().setURL(avatarURL)
            )
    )
    .addSeparatorComponents(new SeparatorBuilder().setDivider(true))
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('### Section 1\nContent here')
    )
    .addSeparatorComponents(new SeparatorBuilder().setDivider(true))
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('### Section 2\nMore content')
    )
    .addSeparatorComponents(new SeparatorBuilder().setDivider(true))
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('-# Footer • Subtitle')
    );
```

---

## ❌ What NOT to Do

```js
// ❌ WRONG — Plain text reply
message.reply('❌ An error occurred.');

// ❌ WRONG — Content property
ctx.reply({ content: '⚠️ Warning message here.' });

// ❌ WRONG — Plain text channel send
channel.send('📋 Log message here');

// ❌ WRONG — Content in channel send
channel.send({ content: 'Notification text' });

// ❌ WRONG — Plain text DM
user.send('Your ticket has been closed.');

// ❌ WRONG — Classic embed
const embed = new EmbedBuilder()
    .setColor(0xFF0000)
    .setDescription('Error');
message.reply({ embeds: [embed] });

// ❌ WRONG — Old ephemeral style
interaction.reply({ content: 'Error', ephemeral: true });

// ❌ WRONG — Embeds with V2 flags
message.reply({ embeds: [embed], flags: MessageFlags.IsComponentsV2 }); // Don't mix!
```

---

## 📝 Quick Reference

| Type | Color | Emoji | Heading Example |
|------|-------|-------|---------| 
| Error | `colors.error` | ❌ | `## ❌ Error` |
| Success | `colors.success` | ✅ | `## ✅ Success` |
| Warning | `colors.warning` | ⚠️ | `## ⚠️ Warning` |
| Info | `colors.primary` | 📋 | `## 📋 Info` |
| No Permission | `colors.error` | ❌ | `## ❌ No Permission` |
| Usage | `colors.error` | ❌ | `## ❌ Usage` |
| No Data | `colors.primary` | 📊 | `## 📊 No Data Yet` |
| Missing User | `colors.error` | ❌ | `## ❌ Missing User` |
| Invalid Input | `colors.error` | ❌ | `## ❌ Invalid Input` |
| Already Exists | `colors.warning` | ⚠️ | `## ⚠️ Already Exists` |
| Not Found | `colors.warning` | ⚠️ | `## ⚠️ Not Found` |
| DM Failed | `colors.error` | ❌ | `## ❌ DM Failed` |

---

## 🔄 Message Method Mapping

Every Discord.js message method must use V2 components:

| Method | Where Used | V2 Required? |
|--------|-----------|--------------|
| `message.reply()` | Prefix commands | ✅ YES |
| `interaction.reply()` | Slash commands | ✅ YES |
| `interaction.editReply()` | Deferred slash commands | ✅ YES |
| `interaction.followUp()` | Additional responses | ✅ YES |
| `channel.send()` | Log channels, notifications | ✅ YES |
| `user.send()` | DM messages | ✅ YES |
| `message.edit()` | Editing bot messages | ✅ YES |

---

## Key Rules Summary
1. **Every reply** → `ContainerBuilder` with `TextDisplayBuilder`
2. **Every send** → Include `flags: MessageFlags.IsComponentsV2`
3. **Every message** → Include `allowedMentions: { parse: [] }`
4. **Headings** → Use `## emoji Title` format inside TextDisplay
5. **Footers** → Use `-# footer text` (small text markdown)
6. **Separators** → Use `SeparatorBuilder().setDivider(true)` between sections
7. **Accent colors** → Always set using `config.colors` based on message type
8. **Ephemeral** → Use `MessageFlags.IsComponentsV2 | MessageFlags.Ephemeral` (not `ephemeral: true`)
9. **Channel sends** → Same V2 pattern as replies (logs, notifications, DMs)
10. **Edit/FollowUp** → Same V2 pattern (editReply, followUp, message.edit)
11. **No mixing** → Never mix `embeds` with `IsComponentsV2` flag
12. **Error handling** → Even catch block error messages must use V2 containers


