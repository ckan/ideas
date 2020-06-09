# Decoupled Read Frontend

- Start Date: 2020-06-09
- Author: Rufus Pollock
- Target Major Version: >= 2.8
- Reference Issues:
- Implementation PR: 

# Summary

This RFC proposes a new pattern for developing (read) frontends for CKAN i.e. all of the display functionality such as front page, search, viewing datasets etc (but not the admin or editing). Key aspects of the pattern are:

* Decoupled: the Frontend is a separate web application that gets its "data" from CKAN's action API (data here means metadata for datasets or content). This means you can use build the frontend with whatever language or tooling you need.
* Modern frontend stack: Take full advantage of modern best-practice frontend stack and approach e.g. Javascript, React/Vue, SSR etc.

In addition we recommend (though it is not an essential to the core point of decoupling):

* Composition (vs inheritance): compositional rather than inheritance-based approach to building up the interface. Compositional means like React or any other framework where you import the components together and glue them together explicitly. By contrast, with inheritance there is a predefined base template with slots which one then fills in.

# Basic Example

See https://github.com/datopian/portal for an in progress implementation using React + Next.js.

# Motivation

Two primary motivations:

* **Developer Experience** - esp speed and ease of development: modern frontend webstack is Javascript based and now highly developed (React, SSR etc). Being able to use that stack to the full (and not have to run CKAN docker) brings a lot of benefits: fewer tools to know, a wider/more developed set of tooling etc. This increases the pool of qualified devs, makes it easier and faster to get started etc.
* **Unified frontends**: peple want to build unified frontend experiences: sites that combine material from multiple sources e.g. material from data catalog and from e.g. a CMS. Modern CMSes face the same needs of combining several sources of content into one unified display and the normal approach is "one head, multiple bodies" (cf Headless CMS). The same approach can be used here.
  ```
      0      # single head

    |   |    # multiple bodies i.e. multiple backend sources
   /\  /\
  ```

➡️  **Decoupled frontend application built in javascript would address both of these needs.**

# Approach

See WIP here: https://github.com/datopian/portal and esp MVP issue: https://github.com/datopian/portal/issues/9 and https://github.com/datopian/portal/issues/16

Some key points:

* Separate application that would run as its own service
* Develop a framework for *creating* separate web frontends. This is a compositional model 
* Javascript based
* Server side rendering so that we get SEO and dynamic pages

Recommendations for base technology:

* React: we evaluated Vue and React. Both are great and our choice here was more determined by general popularity and Next.js (which is React based)
* Next.js: [this is more optional] for SSR so that we get SEO and dynamic pages

## Changes for CKAN Core: None

This approach requires **no** changes in CKAN Core: neither to the API, nor to the current frontend.

Obviously, assuming this approach were successful, longer term it might replace the current frontend which could be deprecated and factored out of CKAN core. 

# Benefits

Developer experience: natural fit for designers and frontend developers plus a rich existing toolset

* Build frontends without specialist knowledge: e.g. knowing python, dealing with complexities of CKAN backend docker etc
* Build frontends fast: bootstrap an app in 5-10m
* Deploy easily: lots of options for deploying JS-based apps
* Customize and extend frontends easily: comp
* Simpler to test and reason about 

Unified, rich frontends

* Integrate content sources other than CKAN easily -- existing suite of backend adapters out there, large amount of examples of integrating React-based frontends
* Javascript and React is natural fit for rich sites using visualization. Integration of viz etc is now seamless

# Drawbacks

* Changes in "2" places: You don't have all your code (frontend and backend) in one repo
* Compositional model means you have to do some frontend work when you add an extension (the extension does not automatically change frontend)
* Refactor existing extensions: existing extensions will need to be refactored.
* Multiple different frontend technologies (?) -- this would only be an issue if we have multiple default options for frontend base (e.g. React *and* Vue) which is unlikely

# Alternatives

* Javascript in CKAN (?): could be possible to run more React-based rendering in CKAN. But this seems hard as CKAN is a python-based setup.
* Do CMS integration into current CKAN

# Adoption Strategy

## Creating a new site

1. Boot your new site use `create-portal-app`. Use it against mock data. When ready.
2. Deploy your CKAN backend
2. Connect to that CKAN backend

## Testing and mocking ...

TODO

## e.g. ckanext-showcase

How would a complex extension like ckanext-showcase work in the new world.

* Backend: You still install the existing ckanext-showcase as it provies the backend and admin UI.
* Frontend: create a new set of (React) components for displaying of the showcase. You then incorporate these components into your portal app wherever you want them.

# Appendix: a more decoupled CKAN

See RFC XXXX

# Appendix: first effort at decoupled frontend aka “Frontend v2

Aside: current decoupled frontend aka "Frontend v2" 
https://github.com/datopian/frontend-v2 and https://tech.datopian.com/frontend/ (next-gen)
 
There were some issues with this approach

* Intentionally followed  CKAN classic quite closely so =>
* Simple expressjs app with nunjucks
* Extension model was express middleware and templates
 
We want to

* Be compositional rather than use inheritance
* Composition: like React or any other framework. I import the components together and glue them together explicitly
* Inheritance: there is a predefined base template with slots which i then fill in
* Take full advantage of modern frontend stack e.g. React/Vue, SSR etc
