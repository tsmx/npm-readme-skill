[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
![npm (scoped)](https://img.shields.io/npm/v/@tsmx/json-tools)
![node-current (scoped)](https://img.shields.io/node/v/@tsmx/json-tools)
[![Build Status](https://img.shields.io/github/actions/workflow/status/tsmx/json-tools/git-build.yml?branch=master)](https://img.shields.io/github/actions/workflow/status/tsmx/json-tools/git-build.yml?branch=master)
[![Coverage Status](https://coveralls.io/repos/github/tsmx/json-tools/badge.svg?branch=master)](https://coveralls.io/github/tsmx/json-tools?branch=master)

# json-tools

> A comprehensive toolkit for analyzing, transforming, and obfuscating JSON objects. Ideal for pre-processing and optimizing JSON data in your AI apps.

🔍 [utility functions](#utility-functions) for basic analytics of JSON objects
 - check complexity
 - determine nesting depth
 - analyze type stats

🔒 [obfucation functions](#obfuscation-functions-obfuscate) to secure sensible data
- credit card numbers (supporting Visa, Mastercard and Amex numbers)
- IP addresses
- values by key
- numbers
- strings
- values/keys by RegEx

⚡ [transformation functions](#transformation-functions-transform) to generate alternative formats out of a JSON object
- array
- map
- properties string
- token-optimized LLM representation

See the [API reference](#api-reference) down below for the full specs and examples.

👉 Practical use-case: Suppose you have the following JSON object that serves as input in your AI application:

```JSON
{
    "accounts": [
        { "id": 1, "name": "Joe", "creditCard": "4111-1111-1111-1111" },
        { "id": 2, "name": "Sue", "creditCard": "5555-5555-5555-4444" }
    ],
    "visits": [
        { "visitorId": 1, "timestamp": "2025-01-01T12:00:00Z", "ip": "192.168.1.1", "site": "index.html"},
        { "visitorId": 1, "timestamp": "2025-01-01T13:05:00Z", "ip": "192.168.1.2", "site": "shop.html"},
        { "visitorId": 2, "timestamp": "2025-01-01T14:00:00Z", "ip": "192.168.1.2", "site": "login.html"},
        { "visitorId": 2, "timestamp": "2025-01-01T14:10:00Z", "ip": "192.168.1.1", "site": "index.html"}
    ]
}
```

Before feeding the data into any LLM, you need to...
- obfuscate sensible data: IP addresses, credit card numbers
- optimize the data representation to save tokens

To achieve this, simply do:

```javascript
const jt = require('@tsmx/json-tools');

jt.obfuscate.ipAddresses(obj);
jt.obfuscate.creditCards(obj);
const result = jt.transform.toLLM(obj, true);
```

This gives you the following result.

```
accounts[2](id,name,creditCard)
 -1
  Joe
  ***
 -2
  Sue
  ***
visits[4](visitorId,timestamp,ip,site)
 -1
  2025-01-01T12:00:00Z
  ***
  index.html
 -1
  2025-01-01T13:05:00Z
  ***
  shop.html
 -2
  2025-01-01T14:00:00Z
  ***
  login.html
 -2
  2025-01-01T14:10:00Z
  ***
  index.html
```

💰 For this example, the token count is reduced from 265 to 139 according to [OpenAI Tokenizer](https://platform.openai.com/tokenizer) for GPT-4o giving you a saving of 48% plus the safety of not exposing sensible data to the LLM.

## API Reference

### Obfuscation Functions (`obfuscate`)

#### `obfuscate.strings(obj, replacement, retain, minreplace)`

Obfuscates all string values in a JSON object by replacing characters with a replacement character while retaining a specified number of left-most characters.

**Parameters:**
- `obj` (Object): The object to obfuscate
- `replacement` (string, optional): The replacement character (default: `*`)
- `retain` (number, optional): The number of left-most characters to retain (default: `3`)
- `minreplace` (number, optional): The minimal number of replacement characters to use (default: `3`)

**Example:**
```javascript
const { obfuscate } = require('@tsmx/json-tools');

const input = {
  firstName: 'John',
  lastName: 'Smith',
  city: 'New York'
};

obfuscate.strings(input);
// Result: { firstName: 'Joh***', lastName: 'Smi***', city: 'New***' }
```

#### `obfuscate.numbers(obj, replacement)`

Obfuscates all number values in a JSON object by replacing them with a given string.

**Parameters:**
- `obj` (Object): The object to obfuscate
- `replacement` (string, optional): The replacement string (default: `***`)

**Example:**
```javascript
const { obfuscate } = require('@tsmx/json-tools');

const input = {
  firstName: 'John',
  age: 30,
  salary: 50000
};

obfuscate.numbers(input);
// Result: { firstName: 'John', age: '***', salary: '***' }
```

#### `obfuscate.ipAddresses(obj, replacement)`

Obfuscates IP address values (IPv4 and IPv6) in a JSON object by replacing them with a given string.

**Parameters:**
- `obj` (Object): The object to obfuscate
- `replacement` (string, optional): The replacement string (default: `***`)

**Example:**
```javascript
const { obfuscate } = require('@tsmx/json-tools');

const input = {
  serverIp: '192.168.1.1',
  clientIp: '10.0.0.5'
};

obfuscate.ipAddresses(input);
// Result: { serverIp: '***', clientIp: '***' }
```

#### `obfuscate.creditCards(obj, replacement)`

Obfuscates credit card values in a JSON object by replacing them with a given string. Supports Visa, MasterCard, and Amex card numbers separated by dashes, dots, whitespaces, or without delimiters.

**Parameters:**
- `obj` (Object): The object to obfuscate
- `replacement` (string, optional): The replacement string (default: `***`)

**Example:**
```javascript
const { obfuscate } = require('@tsmx/json-tools');

const input = {
  cardNumber: '4012-8888-8888-1881',
  cardType: 'Visa'
};

obfuscate.creditCards(input);
// Result: { cardNumber: '***', cardType: 'Visa' }
```

#### `obfuscate.keyRegex(obj, pattern, replacement)`

Obfuscates all values of a JSON object where the key matches a given RegEx pattern (case-insensitive).

**Parameters:**
- `obj` (Object): The object to obfuscate
- `pattern` (string): The RegEx pattern to match keys
- `replacement` (string, optional): The replacement string (default: `***`)

**Example:**
```javascript
const { obfuscate } = require('@tsmx/json-tools');

const input = {
  firstName: 'John',
  lastName: 'Smith',
  email: 'john@example.com'
};

obfuscate.keyRegex(input, 'name');
// Result: { firstName: '***', lastName: '***', email: 'john@example.com' }
```

#### `obfuscate.valueRegex(obj, pattern, replacement)`

Obfuscates all values of a JSON object where the value matches a given RegEx pattern (case-insensitive).

**Parameters:**
- `obj` (Object): The object to obfuscate
- `pattern` (string): The RegEx pattern to match values
- `replacement` (string, optional): The replacement string (default: `***`)

**Example:**
```javascript
const { obfuscate } = require('@tsmx/json-tools');

const input = {
  firstName: 'John',
  city: 'New York',
  country: 'United States'
};

obfuscate.valueRegex(input, 'ork');
// Result: { firstName: 'John', city: '***', country: 'United States' }
```

### Transformation Functions (`transform`)

#### `transform.toMap(obj)`

Converts a JSON object to a JavaScript Map containing all root-level object properties as entries with their property names as keys.

**Parameters:**
- `obj` (Object): The object to convert

**Returns:** Map - A Map containing all root-level properties

**Example:**
```javascript
const { transform } = require('@tsmx/json-tools');

const input = {
  firstName: 'John',
  lastName: 'Smith',
  age: 30
};

const result = transform.toMap(input);
// Result: Map { 'firstName' => 'John', 'lastName' => 'Smith', 'age' => 30 }
```

#### `transform.toArray(obj)`

Converts a JSON object to an Array containing {key, value} objects for all root-level properties.

**Parameters:**
- `obj` (Object): The object to convert

**Returns:** Array - An Array of {key, value} objects

**Example:**
```javascript
const { transform } = require('@tsmx/json-tools');

const input = {
  firstName: 'John',
  lastName: 'Smith',
  age: 30
};

const result = transform.toArray(input);
// Result: 
// [
//   { key: 'firstName', value: 'John' },
//   { key: 'lastName', value: 'Smith' },
//   { key: 'age', value: 30 }
// ]
```

#### `transform.toProperties(obj, expandArrays)`

Converts a JSON object to a properties file string. Nested subobjects and their values are resolved into properties with full dot-separated paths (e.g., `country.name=USA`). Line endings are `\r\n`.

**Parameters:**
- `obj` (Object): The object to convert
- `expandArrays` (boolean, optional): If true, creates an entry for each array element with zero-based index (default: `false`)

**Returns:** String - A properties file formatted string

**Example:**
```javascript
const { transform } = require('@tsmx/json-tools');

const input = {
  firstName: 'John',
  lastName: 'Smith',
  country: {
    name: 'USA',
    code: 'US'
  }
};

const result = transform.toProperties(input);
// Result:
// firstName=John
// lastName=Smith
// country.name=USA
// country.code=US
```

#### `transform.toPropertiesFlat(obj, expandArrays)`

Converts a JSON object to a properties file string considering only the root level. Nested subobjects and arrays are printed as stringified JSON.

**Parameters:**
- `obj` (Object): The object to convert
- `expandArrays` (boolean, optional): If true, creates an entry for each array element with zero-based index (default: `false`)

**Returns:** String - A flat properties file formatted string

**Example:**
```javascript
const { transform } = require('@tsmx/json-tools');

const input = {
  firstName: 'John',
  lastName: 'Smith',
  country: {
    name: 'USA',
    code: 'US'
  }
};

const result = transform.toPropertiesFlat(input);
// Result:
// firstName=John
// lastName=Smith
// country={"name":"USA","code":"US"}
```

#### `transform.toLLM(obj, compactArrays)`

Converts a JSON object to an LLM-friendly, token-saving notation optimized for further processing in AI applications.

**Parameters:**
- `obj` (any): The object to convert
- `compactArrays` (boolean, optional): If true, arrays containing only identical objects (same keys and order) will be further compacted (default: `false`)

**Returns:** String - A token-optimized LLM notation string

**Example 1 - Simple object:**
```javascript
const { transform } = require('@tsmx/json-tools');

const input = {
  firstName: 'John',
  lastName: 'Smith',
  age: 30
};

const result = transform.toLLM(input);
// Result:
// firstName=John
// lastName=Smith
// age=30
```

**Example 2 - Array with non-identical objects (different key order):**
```javascript
const { transform } = require('@tsmx/json-tools');

const input = {
  accounts: [
    { id: 1, name: 'Joe' },
    { name: 'Sue', id: 2 }
  ]
};

const result = transform.toLLM(input);
// Result:
// accounts[2]
//  -id=1
//   name=Joe
//  -name=Sue
//   id=2
```

**Example 3 - Array with identical objects (compacted):**
```javascript
const { transform } = require('@tsmx/json-tools');

const input = {
  accounts: [
    { id: 1, name: 'Joe' },
    { id: 2, name: 'Sue' },
    { id: 3, name: 'Alice' }
  ]
};

const result = transform.toLLM(input, true);
// Result:
// accounts[3](id, name)
//  -1
//   Joe
//  -2
//   Sue
//  -3
//   Alice
```

### Utility Functions

#### `getDepth(obj, includeArrays)`

Retrieves the nesting level (depth) of a JSON object. The root level is considered to be zero.

**Parameters:**
- `obj` (Object): The object to inspect
- `includeArrays` (boolean, optional): Whether to consider objects within arrays when calculating depth (default: `true`)

**Returns:** Number - The zero-based depth of the JSON structure

**Example:**
```javascript
const jt  = require('@tsmx/json-tools');

const simple = { firstName: 'John', age: 30 };
const nested = { user: { firstName: 'John', address: { city: 'NYC' } } };

jt.getDepth(simple); // Result: 0
jt.getDepth(nested); // Result: 2
```

#### `isSimple(obj, includeArrays)`

Checks if a JSON object is simple, meaning it has no nested objects (depth == 0).

**Parameters:**
- `obj` (Object): The object to inspect
- `includeArrays` (boolean, optional): Whether to consider objects within arrays (default: `true`)

**Returns:** Boolean - True if the object is simple

**Example:**
```javascript
const jt  = require('@tsmx/json-tools');

const simple = { firstName: 'John', age: 30 };
const nested = { user: { firstName: 'John' } };

jt.isSimple(simple); // Result: true
jt.isSimple(nested); // Result: false
```

#### `isComplex(obj, includeArrays)`

Checks if a JSON object is complex, meaning it has nested objects (depth > 0).

**Parameters:**
- `obj` (Object): The object to inspect
- `includeArrays` (boolean, optional): Whether to consider objects within arrays (default: `true`)

**Returns:** Boolean - True if the object is complex

**Example:**
```javascript
const jt  = require('@tsmx/json-tools');

const simple = { firstName: 'John', age: 30 };
const nested = { user: { firstName: 'John' } };

jt.isComplex(simple); // Result: false
jt.isComplex(nested); // Result: true
```

#### `typeStats(obj)`

Analyzes all values of an object and returns the number of occurrences per type in a Map. Performs deep parsing including subobjects and array elements.

**Parameters:**
- `obj` (Object): The object to analyze

**Returns:** Map - A Map containing the count for every type found (e.g., `{ 'string' => 5, 'number' => 2, 'object' => 1 }`)

**Example:**
```javascript
const jt  = require('@tsmx/json-tools');

const input = {
  firstName: 'John',
  lastName: 'Smith',
  age: 30,
  active: true,
  country: { name: 'USA' }
};

const result = jt.typeStats(input);
// Result: Map {
//   'string' => 3,
//   'number' => 1,
//   'boolean' => 1,
//   'object' => 1
// }
```

---