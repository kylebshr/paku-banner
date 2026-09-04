# paku-banner

`banner.json` is fetched by Paku at launch (via the `/banner` redirect on
the Paku backend). It is an array of banners; the app shows the first
banner that matches the current build for each placement.

## Fields

| Field | Type | Notes |
|---|---|---|
| `identifier` | string | Unique per banner. Dismissals are stored against it and synced over iCloud, so reuse an identifier only to re-show a banner people already dismissed. |
| `placement` | `"fullScreen"` \| `"favorites"` | Where the banner appears. `fullScreen` presents a modal at launch; `favorites` shows a card at the top of the favorites list. Defaults to `fullScreen` when omitted. See [Favorites banners and old builds](#favorites-banners-and-old-builds). |
| `canBeDismissed` | bool | Full screen: shows a close button and only presents once. Favorites: shows a Dismiss button; the card stays until tapped. `false` on a favorites card keeps it pinned. |
| `version` | object, optional | `{ "comparison": "less" \| "equal" \| "greater", "build": 331 }` compared against the app's build number. Omit to target every build. |
| `symbol` | object, optional | `{ "name": "<SF Symbol>", "color": "red" \| "orange" \| "yellow" \| "green" \| "blue" \| "indigo" \| "purple" \| "primary" \| "secondary" }` |
| `title` | string, optional | |
| `text` | string, optional | |
| `actions` | array | `{ "isPrimary": bool, "title": string, "url": string }`. Each opens its URL. Full screen renders primary actions as prominent buttons; the favorites card tints them. |

## Favorites banners and old builds

Builds up to and including 7.1.2 don't know about `placement` and present
the first matching banner full screen, whatever its placement says. Every
`favorites` banner must therefore carry a `version` rule that excludes
them. The last 7.1.2 build is 1165, so:

```json
{
    "identifier": "purpleair_data_issue_2026_09",
    "placement": "favorites",
    "version": {
        "comparison": "greater",
        "build": 1165
    },
    "canBeDismissed": true,
    "symbol": {
        "name": "wrench.and.screwdriver.fill",
        "color": "blue"
    },
    "title": "PurpleAir Data Issues",
    "text": "We’re investigating an issue loading PurpleAir data.",
    "actions": []
}
```

Local debug builds report build 830, so a rule like this hides favorites
banners when running from Xcode. Drop the `version` rule temporarily to
test one locally.
