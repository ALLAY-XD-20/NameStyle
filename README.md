<div align="center">

# 🪪 Discord Nameplate — Bot Customization Guide

### *Display Name Styles (Fonts, Effects & Colors) for Bot Accounts*

![Discord](https://img.shields.io/badge/Discord-API-5865F2?style=for-the-badge&logo=discord&logoColor=white)
![discord.js](https://img.shields.io/badge/discord.js-v14-5865F2?style=for-the-badge&logo=npm&logoColor=white)
![Status](https://img.shields.io/badge/Status-Working-success?style=for-the-badge)
![Docs By](https://img.shields.io/badge/Docs%20By-ALLAY--XD__20-00FFFF?style=for-the-badge&logo=github&logoColor=black)

</div>

---

## 📖 Overview

Discord's **Nameplate** feature (internally: *Display Name Styles*) lets a guild profile customize how a member's display name is styled inside a specific server — fonts, colors, and glow/neon effects.

Unlike global cosmetics like avatar decorations or profile effects (which require Nitro entitlements and ownership checks), nameplates are **plain member profile metadata**. 🎉

> ✅ **No Nitro required**
> ✅ **No entitlement check**
> ✅ **Works on standard bot accounts**

---

## ⚙️ How It Works Under the Hood

A `namestyle` command sends a direct `PATCH` request to:

```
/guilds/{guild_id}/members/@me
```

This is the same server-profile endpoint used for nicknames, server avatars, banners, and bios.

### 📦 Payload Fields

```js
await client.rest.patch(Routes.guildMember(guildId, "@me"), {
  body: {
    display_name_font_id: fontId,       // 🔤 Numeric font ID
    display_name_effect_id: effectId,   // ✨ Numeric effect ID
    display_name_colors: colors         // 🎨 Array of 1–2 decimal hex colors
  }
});
```

---

## 🎨 Hex → Decimal Color Conversion

> ⚠️ Discord expects **decimal integers**, not hex strings!

```js
function hexToDecimal(hex) {
  // Remove the hash if it exists
  hex = hex.replace('#', '');
  return parseInt(hex, 16);
}

// Usage:
const decimalColor = hexToDecimal('#FF69B4'); // 16738740
```

---

## 🔤 Supported Fonts

| Icon | Font      | ID |
|:----:|-----------|:--:|
| 🔠 | default   | 11 |
| ⏱️ | tempo     | 1  |
| 🌸 | sakura    | 3  |
| 🍬 | jellybean | 4  |
| 🆕 | modern    | 6  |
| 🕹️ | 8bit      | 8  |
| 🧛 | vampyre   | 10 |
| 🏰 | medieval  | 7  |

---

## ✨ Supported Effects

| Icon | Effect   | ID |
|:----:|----------|:--:|
| 🟦 | solid    | 1  |
| 🌈 | gradient | 2  |
| 💡 | neon     | 3  |
| 🎭 | toon     | 4  |
| 🎉 | pop      | 5  |
| 🌟 | glow     | 6  |

---

## 💻 discord.js v14 — Slash Command Example

> 🌸 Applies **Sakura** font + **Glow** effect, colored **#FF69B4** (pink)

```js
const { SlashCommandBuilder, Routes } = require('discord.js');

module.exports = {
  data: new SlashCommandBuilder()
    .setName('namestyle')
    .setDescription('Updates the bot\'s nameplate in this server.'),
  async execute(interaction) {
    await interaction.deferReply({ ephemeral: true });
    try {
      await interaction.client.rest.patch(
        Routes.guildMember(interaction.guildId, "@me"),
        {
          body: {
            display_name_font_id: 3,        // 🌸 Sakura font
            display_name_effect_id: 6,      // 🌟 Glow effect
            display_name_colors: [16738740] // 🎨 Decimal for #FF69B4
          }
        }
      );
      await interaction.editReply('✅ Nameplate updated successfully!');
    } catch (error) {
      console.error(error);
      await interaction.editReply('❌ Failed to update nameplate.');
    }
  }
};
```

---

## 🔄 Removing a Nameplate

To reset the bot's display name back to default, send `0` / empty array:

```js
body: {
  display_name_font_id: 0,
  display_name_effect_id: 0,
  display_name_colors: []
}
```

---

## 🚫 Why Other Cosmetics Fail

| Cosmetic | Field | Result |
|----------|-------|--------|
| 🖼️ Avatar Decoration | `avatar_decoration_sku_id` | ❌ `COLLECTIBLES_NOT_OWNED` — bots can't own shop entitlements |
| ✨ Profile Effect | `profile_effect_id` | ❌ Blocked — restricted to `/users/@me/profile`, which bots can't access |
| 🪪 **Nameplate** | `display_name_*` | ✅ **Works fine!** |

---

## 🛠️ Troubleshooting

### ❗ `400 Bad Request`
Invalid `display_name_font_id` or `display_name_effect_id`. Double-check the [Fonts](#-supported-fonts) and [Effects](#-supported-effects) tables.

### 🔒 `403 Forbidden`
Bot is missing the **MANAGE_NICKNAMES** permission, or its role sits below the target in the hierarchy.

### 📛 `COLLECTIBLES_NOT_OWNED`
Sent an Avatar Decoration or Profile Effect ID by mistake. Stick to `display_name_font_id`, `display_name_effect_id`, and `display_name_colors`.

---

<div align="center">

### 📝 Documentation maintained by **ALLAY-XD_20**

![Made with](https://img.shields.io/badge/Made%20with-%E2%9D%A4%EF%B8%8F-red?style=flat-square)
![Team](https://img.shields.io/badge/Team-Lyrixa-00FFFF?style=flat-square)

</div>
