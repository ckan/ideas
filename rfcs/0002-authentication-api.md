# Authentication API

- Start Date: 2020-04-09
- Author: Rufus Pollock
- Target Major Version: 2.x
- Reference Issues: (fill in existing related issues, if any)
- Implementation PR: (leave this empty)
- Status: Implemented - https://github.com/datopian/ckanext-auth

# Summary

Add an authentication API to CKAN so that CKAN can be used as an auth service in other services and applications.

Note: we already have a sign up API with `user_create`: https://docs.ckan.org/en/2.8/api/index.html#ckan.logic.action.create.user_create

**Working implementation: https://github.com/datopian/ckanext-auth**

# Basic example

A new `user_login` action to the CKAN API so that you can call it for authentication of a user from a third party application:

* Method: POST
* Endpoint: `http://ckan:5000/api/3/action/user_login`
* Body: `{"id": <username>, "password": <password>}`

Example of using it in the NodeJS app:

```javascript
const loginViaCKAN = async function(body) {
   // Call `user_login` action here
}

app.post("/login", async (req, res) => {
   const loggedUser = await loginViaCKAN(req.body)
   if (loggedUser) {
      // Add logged user to session
      req.session.ckan_user = loggedUser
      res.redirect('/dashboard')
   } else {
      req.flash('error_messages', 'Invalid username or password.')
      res.redirect('/login')
   }
})
```

# Motivation

So that CKAN can be used as an auth service in other services and applications.

For example, a decoupled frontend can now implement user login (see [RFC 0005](./0005-decoupled-frontend.md)).

# Approach

This is a natural candidate for an extension. It would just be a new logic action `user_login` (see example above).

# Drawbacks

- One could use the API to bulk try passwords

This is not a new security item since one could do the same via the existing login page in the UI (and its form endpoint). And one can address in the same way: by implementing limits on login attempts etc (see ckanext-security plugin).

# Alternatives

There seems to be no obvious alternative.

# Adoption strategy

Install the extension.

# Unresolved questions

None.
