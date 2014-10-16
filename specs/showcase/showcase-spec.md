# CKAN Showcase extension

Allow users to create, edit and delete related item objects that demonstrate data in-use. These objects can relate to none or many datasets. Related items can be promoted by dataset publishers to feature in a showcase collection linked from their dataset page. Similarly, site admins will be able to promote related items to be listed in a top-level, site-wide showcase.

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
Related Items will be broken out of CKAN core into a separate extension called 'ckanext-showcase' that will make them first-order objects within CKAN.

### A new name
It isn't immediately clear that Related Items refer to examples of data in-use. I suggest that individual Related Items are renamed **Show & Tell** items that can be collected into featured **Showcase** collections by dataset publishers and site sysadmins (perhaps also by Group and Organization owners).

Show & Tell is informal enough to encourage users to share all instances of data reuse, not just what they might consider their best efforts.
It's a verb rather than noun and doesn't imply any specific application (it's not a specific type of reuse like 'Apps & Ideas')
Site admins and dataset publishers can promote the best S&T items by featuring them in Showcase collections.

### Site visitor
Users will be able to search for and discover Show & Tell items from dataset pages, and from the top-level site Showcase. Also from Organization and Group pages, if a Showcase collection feature is integrated at that level.

### All account users
Users with accounts will be able to create Show & Tell items and relate them to none or many datasets. They will be the owners of the S&T item. A page listing the S&T items they have created will be available from their user profile where they can edit and delete the S&T item.

### Dataset publishers
Publishers will be able to feature Show & Tell items in a Showcase linked from their dataset page. Optionally, a dataset owner could allow a link/filter from the dataset Showcase page that shows all S&T items associated with the dataset.

Dataset publishers will be notified when a new S&T item has been created that relates to their dataset, so they can decide whether to promote it in their Showcase. Notifications could reuse the existing activity streams, dashboard and email notifications system.

### Site owners
Site owners will be able to feature Show & Tell items in a site-wide, top-level Showcase linked from the main navigation. This will be search and filterable and link through to individual S&T item pages.

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
