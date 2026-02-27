---
description: Discord.js V2 Components — All bot messages MUST use Components V2 (ContainerBuilder) instead of plain text or classic embeds
---

# Discord.js V2 Components Standard

## 🚨 CRITICAL RULE
**Every single bot message in this project MUST use Discord.js Components V2 (ContainerBuilder).** 
- ❌ NEVER use plain text replies like `message.reply('some text')`
- ❌ NEVER use `{ content: 'some text' }` 
- ❌ NEVER use classic `EmbedBuilder`
- ❌ NEVER use `ephemeral: true` (old style)
- ✅ ALWAYS use `ContainerBuilder` + `TextDisplayBuilder` with `MessageFlags.IsComponentsV2`

---

## Required Imports

```js
const { 
    ContainerBuilder, 
    TextDisplayBuilder, 
    SeparatorBuilder, 
    SectionBuilder, 
    ThumbnailBuilder, 
    MessageFlags 
} = require('discord.js');
```

---

## Color Config (from config.js)

```js
const { colors } = require('../../config'); // or adjust path
// colors.primary  = 0x5865F2 (Blurple)
// colors.success  = 0x57F287 (Green)
// colors.error    = 0xED4245 (Red)
// colors.warning  = 0xFEE75C (Yellow)
// colors.info     = 0x5865F2 (Blue)
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
// Use MessageFlags.Ephemeral combined with IsComponentsV2
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

---

## ❌ What NOT to Do

```js
// ❌ WRONG — Plain text
message.reply('❌ An error occurred.');

// ❌ WRONG — Content property
ctx.reply({ content: '⚠️ Warning message here.' });

// ❌ WRONG — Classic embed
const embed = new EmbedBuilder()
    .setColor(0xFF0000)
    .setDescription('Error');
message.reply({ embeds: [embed] });

// ❌ WRONG — Old ephemeral style
interaction.reply({ content: 'Error', ephemeral: true });
```

---

## 📝 Quick Reference

| Type | Color | Emoji | Heading |  
|------|-------|-------|---------|
| Error | `colors.error` | ❌ | `## ❌ Error` |
| Success | `colors.success` | ✅ | `## ✅ Success` |
| Warning | `colors.warning` | ⚠️ | `## ⚠️ Warning` |
| Info | `colors.primary` | 📋 | `## 📋 Info` |
| No Permission | `colors.error` | ❌ | `## ❌ No Permission` |
| Usage | `colors.error` | ❌ | `## ❌ Usage` |
| No Data | `colors.primary` | 📊 | `## 📊 No Data Yet` |

---

## Key Rules Summary
1. **Every reply** → `ContainerBuilder` with `TextDisplayBuilder`
2. **Every send** → Include `flags: MessageFlags.IsComponentsV2`
3. **Every message** → Include `allowedMentions: { parse: [] }`
4. **Headings** → Use `## emoji Title` format inside TextDisplay
5. **Footers** → Use `-# footer text` (small text markdown)
6. **Separators** → Use `SeparatorBuilder().setDivider(true)` between sections
7. **Accent colors** → Always use from `config.colors` based on message type
8. **Ephemeral** → Use `MessageFlags.IsComponentsV2 | MessageFlags.Ephemeral` (not `ephemeral: true`)
