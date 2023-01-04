# Authentication

> The authentication module is responsible for handling the user authentication, registration, login and in general all that is neccessary for the user to safely access the system

## General

The data part of the authentication module handles encrypting user sensitive data (such as emails and passwords), by using the public key that the server provides.

It also handles the JWT refreshes, cookies and force log-out when the token expires or a request fails more than 3 times.

> Due to time constraints, tests were not included. To be implemented in the future... maybe...

## Architecture

> ℹ️ The Authentication module depends on the `data`,`nav` and `ui` modules. The module's responsibility is to handle the logic of registration, login and password reset.

### States

> The authentication states of the module are split into the `RegistrationState`, `LoginState`, `ForgotState`
> Each of these states, are interfacing the `Error`, `Success`, `Loading` and `Idle` UI states. These states are important, as they are used to render the UI.

### Events

Each use case has its own events for handling user input. These events are sent to the `ViewModel` by the UI layer (namely the Composables) and the `ViewModel` will process them and produce a UI state.

#### Registration

- `RegistrationEvent.Register` this event signals the `ViewModel` to generate a new User.
- `RegistrationEvent.Idle` this event signals the `ViewModel` to generate an `Idle` state and reset the UI.

#### Login

- `LoginEvent.Login` this event signals the `ViewModel` to sign in the user
- `LoginEvent.GoogleLogin` this event signals the `ViewModel` to either register or sign in a Google user.
- `LoginEvent.Error` this event signals the `ViewModel` to generate an `Error` state. This event is used to generate states based on google's `AsyncTask` failures.
- `LoginEvent.Idle` this event signals the `ViewModel` to generate an `Idle` state and reset the UI.

#### Forgot Password flow

> ⚠️ Potential refactoring

This flow is very simple and thus doesn't have any events. Instead it calls its `ViewModel` methods directly.

However, it has 2 functions.

#### Request password reset

If no deep link argument for userId or password reset token is found. Then show a Composable with an email field for requesting a password reset link.

#### Password reset

If both a userId and a password reset token are found, then show a composable with 2 fields for setting a password and confirming the password respectively.

When the password is reset succesfully, then redirect to the login screen.
