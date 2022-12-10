# Create File

Create schema.json to apply required property => path
(Not use prompt, we pass value with arguments)
```json
{
    "$schema": "http://json-schema.org/schema",
    "$id": "my-collection-create-file",
    "title": "Creates a file using the given path",
    "type": "object",
    "properties": {
      "path": {
        "type": "string",
        "description": "The path of the file to create."
      }
    },
    "required": ["path"] // <========
  }
```

Create schema.ts file to implement interface to available optional and required options:
```typescript
export interface CreateFileOptions {
    path: string;
}
```

Add reference that we use with schema.json in **collection.json**:

```json
{
  "$schema": "../node_modules/@angular-devkit/schematics/collection-schema.json",
  "schematics": {
    ...
    "create-file": {
      "description": "Schematic to work with create files option",
      "factory": "./01-create-file/index#createFile",
      "schema": "./01-create-file/schema.json"
    }
  }
}
```

Test cases:

```bash
schematics .:create-file
```
Output:
```css
An error occured:
Error: Schematic input does not validate against the Schema: {}
Errors:

  Data path "" must have required property 'path'.
    at MapSubscriber.project (/usr/local/lib/node_modules/@angular-devkit/schematics-cli/node_modules/@angular-devkit/schematics/tools/schema-option-transform.js:30:27)
    at MapSubscriber._next (/usr/local/lib/node_modules/@angular-devkit/schematics-cli/node_modules/rxjs/internal/operators/map.js:49:35)
```

```bash
schematics .:create-file --path=test-value.ts
```

Result OK:
```css
CREATE test.ts (44 bytes)
Dry run enabled by default in debug mode. No files written to disk.
```

Now change "test.ts" reference with "path" value.

src/01-create-file/index.ts

```typescript
export function createFile(_options: CreateFileOptions): Rule {
  return (tree: Tree, _context: SchematicContext) => {
    tree.create(_options.path, "console.log('File created from schematic!');");
    return tree;
  };
}
```
Execute compile option:
```
yarn build
```

Execute schematic with argument path:

```bash
schematics .:create-file --path=test-value.ts
```
Result:

```css
Debug mode enabled by default for local collections.
CREATE test-value.ts (44 bytes)
Dry run enabled by default in debug mode. No files written to disk.
```
