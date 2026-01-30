# 6. Validation and Transformation

This lesson is about keeping a set of rules or guidelines in mind when you are designing your APIs.

Mostly related to data integrity and security. 

**DATA NEEDS TO BE IN EXPECTED FORMATED FOR SERVER TO EXECUTE WITH**

A SIMPLIFIED API call looks like this basically:

API Endpoint → (Controller → Service → Repository) → controller → back to client 

Controller Layer

- calls method that executes the business logic
- calls the service layer
- we use controllers and services to keep the HTTP related stuff in a different layer (like error codes, success codes, expected data format, validations needed)
- deals with data coming and going from client

Service Layer

- executes business logic
- can call repository methods

Repository Layer - Bottom layer

- persistent storage
- DB

Validation and transformations happen right before the data (usually JSON) reaches the controller.

so now:

API Endpoint → (validation and transformations) → (Controller → Service → Repository) → controller → back to client 

### Validation

The data the clients are sending (params, headers, payload); before the enter the controller, we want to make sure the data is in the EXPECTED FORMAT that the server can work with

For example:

- an api endpoint takes in two specific fields in a payload like:
    
    ```jsx
    {
    	"requiredField1": "string",
    	"requiredField2": "string"
    }
    ```
    

The validation and transformation layer checks that payload has these required fields AND their values are strings (correct data format). If the payload format doesn’t match 100%, it’ll return an error before any business logic execution is even done.

We need this layer towards the beginning because it can cause issues with bottom layers down the line. We don’t want to get errors at the DB level.

You can have syntactic, semantic, and type validation.

Syntactic like an email:

```jsx
string@email.com
// if there is no @, or .com, or string before the @ then this is a syntactic error
```

Semantic like incorrect date:

```jsx
// lets say you are inserting a date field in the DB that only accepts data in the past
if you add a date in the field that is in the future
// our validation needs to reject it before it reaches the DB
```

Type validation like data match:

```jsx
{
		"field": int
}
// if the value is a string instead, we reject and say we require and int
```

To be clear, when we REJECT a request because of validation, we should respond with the correct format required for the data, like:

```jsx
{
	"error": "Missing requiredField2"
}
{
	"error": "Expected number, recieved string"
}
// these are incredibly basic examples but should get the idea across
```

### Transformation

Transformation converts data into specific format *AFTER* validation is done.

Execute operations on the data.

For example:

```jsx
// in paginated apis, we send different query params
// like
'/bookmarks?page=2&limit=20'

// we need to validate our query params
// the "page" field needs to be A NUMBER and LESS THAN 500
// the "limit" field needs to be a NUMBER and LESS THAN 10000

NOTE: QUERY PARAMS ARE ALWAYS RECEIVED AS STRINGS
// so we would get "2" and "20"
// technically our validation would reject
// BUT we KNOW that this isn't a client error, the values will always be in string format
// its our/the servers responsibility to cast (change) the data type into INT

THIS IS CALLED A TRANSFORMATION
```

Another example could be a phone number. Lets say the user inputs it as simply digits, but your database wants it with dashes. This is where a transformation could be used.

### Difference between Frontend Validation and Serverside Validation

Usually we do validation in forms on the front end.

for example the form would simply state “please input a number” etc.

This is for the USER EXPERIENCE, required to indicate to the user that the data is incorrect.

**BUT we still need serverside validation, because sometimes our API’s aren’t called through a user interface.**

We still need serverside validation for security and data integrity. When designing API’s it doesn’t matter what the client validation look like, you need to make sure the backend is secure.