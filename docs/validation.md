Contentful Migration – Validations
=====

Here is a list of supported validations.

## INVALID_PROPERTY_NAME
Whenever trying to set a property which doesn't exist, and we couldn't offer any suggestion to use as a replacement.

**Example:**
```javascript
field.fdskljhdf('Ctrl + arm')
```

## INVALID_PROPERTY_NAME_WITH_SUGGESTION
Whenever trying to set a property which doesn't exist, and we could offer a suggestion to use as a replacement.

**Example:**
```javascript
field.nmae('First name')
```

## INVALID_PROPERTY_TYPE
Whenever setting a value to a property which is not matching the expected type.

**Example:**
```javascript
field.name(true)
```

## FIELD_ALREADY_CREATED
Whenever trying to create twice the same field.

**Example:**
```javascript
contentType.createField('name').name('First name')

contentType.createField('name').name('Last name')
```

## FIELD_ALREADY_DELETED
Whenever trying to delete a field twice.

**Example:**
```javascript
contentType.deleteField('name')

contentType.deleteField('name')
```

## FIELD_DOES_NOT_EXIST
Whenever trying to edit a field which doesn't exist.

**Example:**
```javascript
contentType.createField('name')
// ...
contentType.editField('nmae')
```

## CONTENT_TYPE_ALREADY_CREATED
Whenever trying to create a content type twice.

**Example:**
```javascript
migration.createContentType('author').name('Author')
// ...
migration.createContentType('author').name('Editor')
```

## CONTENT_TYPE_ALREADY_EXISTS
Whenever trying to create a content type which already exists in the target space.

**Example:**
```javascript
// While `author` already exists in the target space
migration.createContentType('author').name('Author')
```

## CONTENT_TYPE_NOT_YET_CREATED
Whenever trying to edit a content type which doesn't exist.

**Example:**
```javascript
// While `author` already exists in the target space
migration.editContentType('inexistent')
```

## CONTENT_TYPE_DOES_NOT_EXIST
Whenever trying to create or edit a field of a content type which hasn't yet been created.

**Example:**
```javascript
author.createField('fullName').name('Full name')

const author = migration.createContentType('author').name('Author')
```

## REQUIRED_PROPERTY
Whenever a required property is missing in the definition, such as, for example, `name` for fields of content types.

**Example:**
```javascript
const author = migration.createContentType('author')
```

## TOO_MANY_FIELDS
Whenever hitting the 50 fields limit on a content type, while trying to create a new field.

**Example:**
```javascript
const author = migration.createContentType('author')
  .name('Author')

for (let i = 0; i < 60; i++) {
  author.createField('dummy-' + i)
    .name('Dummy field ' + i)
    .type('Symbol')
}
```

## NON_EXISTENT_DISPLAY_FIELD
Whenever setting the `displayField` of a content type to an ID of a field which doesn't exist.

**Example:**
```javascript
const author = migration.createContentType('author')
  .name('Author')
  .displayField('fullName')

author.createField('firstName')
  .name('First name')
  .type('Symbol')

author.createField('lastName')
  .name('Last name')
  .type('Symbol')
```

## DELETE_DISPLAY_FIELD
Whenever trying to delete the field used as the `displayField` of the content type.

**Example:**
```javascript
const author = migration.createContentType('author')
  .name('Author')
  .displayField('fullName')

author.createField('fullName')
  .name('Full name')
  .type('Symbol')

author.deleteField('fullName')
```

## REQUIRED_DEPENDENT_PROPERTY
Whenever editing an element, but forgetting to set a required property, such as `items` for fields of type `Array` or `linkType` for fields of type `Link`.

**Example:**
```javascript
const author = migration.createContentType('author')
  .name('Author')

author.createField('articles')
  .name('Written articles')
  .type('Array')

// Missing the `items` property
```

## FORBIDDEN_DEPENDENT_PROPERTY
Whenever using a property which is not compatible with the type of the field.

**Example:**
```javascript
const author = migration.createContentType('author')
  .name('Author')

author.createField('fullName')
  .name('Full name')
  .type('Symbol')
  .items({ type: 'Asset' })

// `items` is not compatible with field type `Symbol`.
```

## PROPERTY_MUST_BE_ONE_OF
Whenever trying to set a field type with a value which is not allowed.

**Example:**
```javascript
author.createField('fullName')
  .name('Full name')
  .type('Wrong')
```

Allowed types are listed in the [`createField`](#createfieldid--string-opts--object--field) documentation.

## NO_DELETE_WITHOUT_OMIT
Whenever using `deleted(true)` on a field which hasn't been omitted first (using `omitted(true)`).

**Example:**
```javascript
const author = migration.editContentType('author')

// Missing the following code:
// author.editField('fullName')
//  .omitted(true)

author.editField('fullName')
  .deleted(true)
```

Note: you may use the [`deleteField`](#deletefieldid--string--void) shorthand method to do both operations at once.

## NO_TYPE_CHANGE
Whenever trying to edit the `type` of an existing field.

**Example:**
```javascript
author.createField('fullName')
  .name('Full name')
  .type('Symbol')

author.editField('fullName').type('Text')
```

Field types can't be changed, you must disable and omit the field, and create a new field with the right type and a different ID instead.