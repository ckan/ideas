# CKAN Showcase extension

Allow users to create, edit and delete related item objects that demonstrate data reuse. These objects can relate to none or many datasets. Related items can be promoted by dataset publishers to feature in a showcase collection linked from their dataset page. Similarly, site admins will be able to promote related items to be listed in a top-level, site-wide showcase.

Previous discussion of the current situation and a proposed new approach for a Related Item extension can be found here: [github.com/ckan/ckan/wiki/Spec:-Related-Items](https://github.com/ckan/ckan/wiki/Spec:-Related-Items).


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
It isn't immediately clear that 'Related Items' refer to examples of data reuse. For now, we'll refer to an individual Related Item as a **Reuse**. Reuses can be featured by site maintainers and thus collected into **Showcase** collections. A site-wide top-level showcase would list all featured reuses in the site. A dataset showcase, linked from the main dataset page, would list all reuses associated with that dataset. (Perhaps also grouped with Groups and Organizations.)


### Roles

#### Site visitor
Users will be able to search for and discover reuses from dataset pages, and from the top-level site Showcase. Also from Organization and Group pages, if a Showcase collection feature is integrated at that level.

#### Data reuser
A data reuser will be able to submit/add Reuse items and relate them to none or many datasets. A data reuser may be able to have a user account that lists they're reuse items.

#### Dataset publishers
Publishers will be able see featured Reuse items associate with their dataset in a Showcase linked from their dataset page.

#### Site maintainers
Site maintainers will be able to feature Reuse items in a site-wide, top-level Showcase linked from the main navigation. This will be search and filterable and link through to individual Reuse item pages. Site maintainers will need an admin page listing all Reuse items allowing them to promote or remove them if necessary.

Site maintainers will be notified when a new Reuse item has been submitted/added so they can decide whether to feature it. Notifications could reuse the existing activity streams, dashboard and email notifications system.


### User stories

These user stories have been taken from [github.com/ckan/ckan/wiki/Spec:-Related-Items#user-stories](https://github.com/ckan/ckan/wiki/Spec:-Related-Items#user-stories)

#### As a Data reuser, I want to...
* show the cool things I've done with a site's data so that my reuses reach a wider audience.
* be able to associate multiple datasets with each of my data reuses so that I can represent all of the datasets that each of my reuses uses.

#### As a Site visitor, I want to...
* see any re-uses that have been made with this/these dataset(s) since they may be more useful to me than just the data itself.
* be able to see what datasets this data reuse was made from, to understand who data may be used together.
* discover what data re-uses exist so that I can get ideas for myself or because I'm looking for interesting re-uses.
* be able to search through all the data re-uses so that I can see if what I need (e.g. an app about hospital ratings in London) already exists.

#### As a Data publisher (or Org and Group owner), I want to...
* see what reuses have been made from my data so that I can see what value is being made of my data.
* be notified when new reuses have been made from my data so that I can highlight them to site visitors.
* be able to filter the reuses made of datasets to particular time periods so I can use the information in reports to management or funding agencies to demonstrate the value of our research data to external users.
* be able to control which data reuses are displayed with my dataset, so I'm only featuring the best ones.
* see what reuses have been made from my data so that I might get ideas for other research I can do to add further value.
* see what reuses have been made from my data to make connections for future research collaboration (with the reuser).
* be able to report users who abuse or spam using the related item system, so the quality of content is maintained.
