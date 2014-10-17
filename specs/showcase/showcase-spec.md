# CKAN Showcase extension

Allow site maintainers to create, edit and delete Showcase objects that demonstrate the use of data from the CKAN instance. For example, in an app, website or visualization, or being written about in an article, report or blog post. These objects can relate to none or many datasets within the CKAN instance. Showcase items will be listed in a Showcase collection linked from the top-level navigation. Similarly, a link from each individual dataset will list Showcase items making use of data from that dataset. Examples of data use may be submitted for inclusion by anyone who visits the site, and selected for inclusion by site maintainers.

Previous discussion of the current situation (Related Items) and a proposed new approach for an extension can be found here: [github.com/ckan/ckan/wiki/Spec:-Related-Items](https://github.com/ckan/ckan/wiki/Spec:-Related-Items).


## Current implementation
Currently, Related Items exist in CKAN core. A summary of the current technical implementation can be seen here: [github.com/ckan/ckan/wiki/Spec:-Related-Items#summary-of-the-current-implementation](https://github.com/ckan/ckan/wiki/Spec:-Related-Items#summary-of-the-current-implementation).


### Some problems with the current implementation:
* It's hard for users to discover Related Items by search and browsing
* Related Items can't be associated with more than one dataset
* Current Related Item types are predefined and vague (eg. App, Idea, Paper, Post)
* Related Item descriptions are limited in room in the front-end
* Site-wide Related Item collections (Apps & Ideas) don't show to which datasets they relate
* Related Items aren't first-order objects and don't have their own pages
* Related Items can't be extended with extra fields


## Proposal
Related Items will be broken out of CKAN core into a separate extension called 'ckanext-showcase' that will make them first-order objects within CKAN, and resolve issues with the current implementation.


### Naming the feature
It isn't immediately clear that 'Related Item' refers to an instance of data being used outside of CKAN. A collection of items is a **Showcase**, with an individual item being referred to as a Showcase item. Hopefully, there will be little need to refer to individual Showcase items within the site, and where it is necessary (tooltips and buttons such as, "Add Item"), the context should make it clear what kind of item is being referred to.


### Implement in stages
The extension could be implemented in stages that build on each other, each stage finishing up a complete and useful feature.

Stage 1: Related Items would be pulled out of core and reimplemented as an extension, giving each item its own page that links to its associated datasets, and each dataset a list of associated Showcase items. At this point only site maintainers would be able to create Showcase items. All newly created items are publicly viewable from the top-level showcase and from associated datasets.

Stage 2: Some way for the public to submit or directly add (inactive) Showcase items. See below for possible methods of submission. Note that some of these methods may require notification and moderation by a site admin before an item becomes publicly accessible on the website.

Stage 3: A standalone Showcase site where the community of data users can add examples of dataset use from many separate sources. An extension running on each CKAN site may interact with the standalone Showcase to update its own catalogue of Showcase items.


### Adding Showcase items
There are a number of possible ways to add items to the Showcase in an CKAN instance. These are listed in order of increasing complexity to implement.

1. Directly request submissions and provide an email address or link to the website's general contact form. Site maintainers collect the info and create new Showcase items for the instance.

2. A Showcase submission form (no login required) collects item details and creates an inactive Showcase item and sends it to a moderation queue. A site maintainer will use a simple moderation UI to sort through and publish appropriate submissions.

3. Users can create a CKAN account and add their own Showcase items directly to a moderation queue. Site admins are notified of new items and will need to approve them for publication on the site.

4. Users can create a CKAN account and add Showcase items that are published on the site without moderation by a site maintainer. Items are community-moderated with a 'Report abuse' flag. This may require a large and active user community.

3 & 4 would require users to have an account for submitting, and potentially administrating, their Showcase items.


### Roles

#### Site visitor
Users will be able to search for and discover Showcase items from dataset pages, and from the top-level site Showcase. Also from Organization and Group pages, if a Showcase collection feature is integrated at that level.

#### Data user
A data user will be able to submit/add Showcase items and relate them to none or many datasets.

#### Dataset publishers
Publishers will be able see published Showcase items associate with their dataset in a Showcase linked from their dataset page.

#### Site maintainers
Site maintainers will be able to add and publish Showcase items in a site-wide, top-level Showcase linked from the main navigation. This will be search and filterable and link through to individual Showcase item pages. Site maintainers will need an admin page listing all Showcase items allowing them to publish, edit or remove them if necessary.

Site maintainers will be notified when a new Showcase item has been submitted/added so they can decide whether to publish it. Notifications could reuse the existing activity streams, dashboard and email notifications system.


### User stories

These user stories have been taken from [github.com/ckan/ckan/wiki/Spec:-Related-Items#user-stories](https://github.com/ckan/ckan/wiki/Spec:-Related-Items#user-stories)

#### As a Data reuser, I want to...
* show the cool things I've done with a site's data so that my data use reaches a wider audience.
* be able to associate multiple datasets with each of my data uses so that I can represent all of the datasets I used.

#### As a Site visitor, I want to...
* see any how this/these dataset(s) have been used since they may be more useful to me than just the data itself.
* be able to see what datasets this data use was made from, to understand who data may be used together.
* discover how data has been used so that I can get ideas for myself or because I'm looking for interesting uses.
* be able to search through all the data uses so that I can see if what I need (e.g. an app about hospital ratings in London) already exists.

#### As a Data publisher (or Org and Group owner), I want to...
* see what uses has been made from my data so that I can see what value is being made of my data.
* be notified when new uses have been made from my data so that I can highlight them to site visitors.
* be able to filter the uses made of datasets to particular time periods so I can use the information in reports to management or funding agencies to demonstrate the value of our research data to external users.
* be able to control which data uses are displayed with my dataset, so I'm only featuring the best ones.
* see what use has been made from my data so that I might get ideas for other research I can do to add further value.
* see what use has been made from my data to make connections for future research collaboration (with the user of the data).
* be able to report users who abuse or spam using the Showcase item system, so the quality of content is maintained.
