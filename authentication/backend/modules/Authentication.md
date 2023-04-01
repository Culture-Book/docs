# Authentication

> The authentication module is responsible for handling the user authentication, registration, login and in general all that is neccessary for the user to safely access the system

## Architecture

> The Authentication module is architectured by layer of function. As such, the module is split into the data layer, the logic layer and the route layer.

### Data layer

The data layer holds all the database implementation, the data models and the database access object interfaces, the authentication states as well as any other data model the authentication module needs.

It is worth mentioning that the data layer uses a facade design pattern to abstract the database implementation.

> ⚠️⚠️ Take extra care when naming the data models, as the variable names will be serialized to JSON for transfer.

### Logic Layer

The logic layer is where the data processing happens. This is also where the use cases take place.

#### Use cases of the Authentication layer

- Registration
  > ⚠️ This flow doesn't verify users, this is done for the sake of simplicity and time, might need to add this functionality later.
  - Decode the email and password that the API received using the private key.
    - If the email isn't valid return `AuthError.InvalidEmail`
    - If the password isn't valid return `AuthError.InvalidPassword`
    > ⚠️ The decryption process will fail if the public key was incorrect and the same errors will be returned.
  - Check if the email exists
    - If it does, return `AuthError.DuplicateEmail`
  - Create a user with that information.
    - If the database object was not created succesfully, return an `AuthError.DatabaseError`
  > ℹ️ The following is the general JWT creation flow
  - Generate a random access token and a random refresh token.
  - Generate a JWT encoding the userId, and the above tokens
    - If that fails return `AuthError.Generic`
  - Attempt to save the newly created `UserToken` in the database.
    - If that fails return `AuthError.Generic`
  - Finally return the JWT, and refresh tokens to be sent to the user in the registration route.

- Login

  - Decode the email that the API received using the private key.
    - If not valid return `AuthError.InvalidEmail`
  - Check if a user exists with that email.
    - If no user exists, return `AuthError.Generic` (this is to abide by GDPR, the system can't tell if the email is registered or not)
  - Decode the password from the database and the call using the pricate key, and check if they match.
    - If they don't match return `AuthError.Generic` (this is to abide by GDPR, the system can't tell if the password was correct or not)
  - Delete existing user tokens from the database.
  - Generate new JWT keys and return them using the afforementioned jwt flow.

- Register or Login
  - To support third party registration/login use cases (eg. Google Sign-In)
  - When a call is received, decrypt the email.
  - Check if it exists
    - If it does, call the `login` logic
    - Otherwise, call the `registration` logic.

- Send Email
  - This use case is mainly to be used when sending a "reset password" email to the users.
  - By supplying a subject, a message, an email and the email's account configuration.
  - Attempt to send an email

- Forgot password
  - When a user requests a reset a password link, and the API was provided with an email address.
  - Decrypt the email address that was received.
  - Get a user from that address.
  - Create a random reset token.
  - Add 30 minutes to the expiration time.
  - Create an app link for the android application, following this scheme: `https://api.culturebook.co.uk/forgot/$email/$passwordResetToken`
  - Wait 1 minute (This is because the IONOS email service only allows for 1 email per minute)
  > ⚠️ This is a very junky solution, a better one would be to introduce a queue system and dequeue every minute, but for the sake of simplicity and time, this is it.
  - Send email to registered user email.
