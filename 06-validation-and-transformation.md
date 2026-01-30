# 6. Validation and Transformation

## Overview

This lesson covers the essential rules and guidelines for designing APIs, primarily focusing on data integrity and security. The key principle is: **DATA NEEDS TO BE IN EXPECTED FORMAT FOR SERVER TO EXECUTE WITH**.

## API Request Flow

A simplified API call follows this structure:

```
API Endpoint → (Controller → Service → Repository) → Controller → Back to Client
```

### Layer Responsibilities

#### Controller Layer
- Calls methods that execute business logic
- Calls the service layer
- Handles HTTP-related concerns (error codes, success codes, expected data format, validations)
- Manages data coming from and going to the client

#### Service Layer
- Executes business logic
- Can call repository methods

#### Repository Layer (Bottom Layer)
- Handles persistent storage
- Database operations

### Validation and Transformation in the Flow

Validation and transformations happen **before** the data (usually JSON) reaches the controller:

```
API Endpoint → (Validation and Transformations) → (Controller → Service → Repository) → Controller → Back to Client
```

## Validation

Validation ensures that data sent by clients (params, headers, payload) is in the **expected format** that the server can work with, before it enters the controller.

### Why Validation Matters

- Prevents errors from propagating to lower layers
- Catches issues before business logic execution
- Avoids database-level errors
- Ensures data integrity and security

### Types of Validation

#### 1. Syntactic Validation

Checks the structure and format of data.

**Example: Email Validation**
```javascript
string@email.com
// Must have: @ symbol, .com extension, characters before @
// Missing any of these = syntactic error
```

#### 2. Semantic Validation

Checks the meaning and logical correctness of data.

**Example: Date Validation**
```javascript
// If a field only accepts dates in the past
// but a future date is provided
// Validation must reject it before it reaches the DB
```

#### 3. Type Validation

Checks that data types match expected types.

**Example: Type Matching**
```javascript
{
    "field": int
}
// If the value is a string instead, reject with:
// "Expected number, received string"
```

### Validation Example

Consider an API endpoint that expects:

```javascript
{
    "requiredField1": "string",
    "requiredField2": "string"
}
```

The validation layer checks:
- Both required fields are present
- Both values are strings (correct data type)
- If format doesn't match 100%, return an error before any business logic execution

### Validation Error Responses

When rejecting a request due to validation, respond with clear error messages:

```javascript
{
    "error": "Missing requiredField2"
}

{
    "error": "Expected number, received string"
}
```

## Transformation

Transformation converts data into a specific format **AFTER** validation is complete. It executes operations on the data to prepare it for processing.

### Transformation Example 1: Query Parameters

```javascript
// Paginated API with query params
'/bookmarks?page=2&limit=20'

// Validation requirements:
// - "page" must be a NUMBER and LESS THAN 500
// - "limit" must be a NUMBER and LESS THAN 10000

// NOTE: QUERY PARAMS ARE ALWAYS RECEIVED AS STRINGS
// We receive "2" and "20" as strings
// Technically, validation would reject strings
// BUT this isn't a client error - values are always in string format
// It's the server's responsibility to cast (change) the data type to INT

// THIS IS CALLED A TRANSFORMATION
```

### Transformation Example 2: Phone Number Formatting

If a user inputs a phone number as digits only, but your database requires dashes, a transformation would convert:

```
Input:  1234567890
Output: 123-456-7890
```

## Frontend vs. Serverside Validation

### Frontend Validation

- Implemented in forms on the client side
- Purpose: **User Experience**
- Examples: "Please input a number", "Email format required"
- Provides immediate feedback to users

### Serverside Validation

**Critical Note**: You must still implement serverside validation because:

1. **APIs aren't always called through a user interface**
   - Direct API calls
   - Third-party integrations
   - Mobile applications
   - Automated systems

2. **Security and Data Integrity**
   - Frontend validation can be bypassed
   - Malicious users can send direct requests
   - Backend must be the final authority

**Design Principle**: When designing APIs, it doesn't matter what the client validation looks like - you need to make sure the backend is secure.

## Best Practices

1. **Always validate on the server** - Never trust client-side validation alone
2. **Validate early** - Before data reaches business logic or database layers
3. **Provide clear error messages** - Help clients understand what went wrong
4. **Transform after validation** - Ensure data is valid before transforming it
5. **Handle edge cases** - Account for different input formats and types
6. **Maintain data integrity** - Prevent invalid data from entering your system
