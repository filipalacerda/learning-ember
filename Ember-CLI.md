## Comands

| full command     | alias     |
| ---------------- | --------- |
| `ember generate` | `ember g` |
| `ember test`     | `ember t` |
| `ember serve`    | `ember s` |
| `ember version`  | `ember v` |

## Generate files

```bash
ember generate <type-of-file> <name-of-your-choice>
```

1. Example: `ember generate route about` will generate:
   1. `app/templates/about.hbs`
   2. `app/routes/about.js`
   3. `tests/unit/routes/about-test.js`

## Testing your app

```bash
ember test --server
```

## Building the app

```bash
ember build --environment production
```
