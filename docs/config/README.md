---
sidebar: auto
prev: /
---

# Config

## dateFormat

- Type: `string`
- Default: `'ddd MMM DD YYYY'`

The [date](./front-matter.md#date) will be displayed in the layout with this format.
You can find all available formats [here](https://github.com/iamkun/dayjs/blob/dev/docs/en/API-reference.md#displaying)

e.g.

```js
module.exports = {
  themeConfig: {
    dateFormat: 'YYYY-MM-DD'
  }
}
```
