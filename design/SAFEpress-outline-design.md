# SAFEpress Outline Design
This is an initial outline design for SAFEpress, a WordPress like website Content Management System (CMS) for SAFE Network, which is a secure, anonymous, decentralised internet with its own built in serverless storage, messaging and end-to-end encryption. 

Users will be able to access the SAFE (Secure Access For Everyone) internet alongside the existing internet, seamlessly via a browser plugin. Visiting "safe:" website addresses for the former and "http://" website addresses for the latter. 

But for users to be able to access "safe:" websites, someone has to build them, and SAFEpress is a tool for building websites, blogs and web apps that load directly from the unhackable, DDoS, censorship and surveillance proof SAFE Network (for more on SAFE Network visit http://maidsafe.net).

## SAFEpress Design Summary

Differences between the current internet and SAFE Network mean that the implementation of SAFEpress will differ substantially from WordPress, but given WordPress' best of breed status and enormous popularity, we've chosen to use it as our main model for the features and operataion of a website CMS. 

The differences arise because of the decentralised serverless nature of SAFE Network. In terms of implementation, SAFEpress will be more like a CouchApp, where the backend is a simple NoSQL document store, and complex operations are carried out on the client (in javascript for SAFEpress/CouchApp) rather than the server (PHP and SQL for WordPress). So while SAFEpress is likely to have pages, posts, comments, plugins, themes, users, roles and so on, because these are needed in a good functional CMS and blog, the way these are implemented and accessed will be different.

## What is in SAFEpress?
SAFEpress will define the data types, data structures, relationships and meaning, and the operations that can be performed on data.

As now, it will be up to website developers to determine how data and operations are presented in a UI. 
Initially the SAFEpress UI will likely be very simple, and the design customisations all but non-existent. Perhaps limited to a few examples (themes) for different use cases (blog, website, forum..). 
Developers will create the look, feel and functionality of a website by modifying the UI side of these examples, or writing their own from scratch. 

Support for data access and manipulation will be through a client-side JavaScript API, that performs core SAFEpress operations by accessing storage and messaging on SAFENetwork. The SAFEpress core API is equivalent to the WordPress core API, and not normally touched by a website developer. In the case of SAFEpress the core API is a javascript include, rather than server side PHP and SQL.

### SAFEpress Architecture

The SAFEpress project is actually two things:

- the SAFEpress application (a WordPress like content management system for SAFE Network)
- the SafeApp Web Framework (a noBackend Dreamcode API for building web apps on SAFE Network)

Both depend on a web browser plugin that intercepts "safe:" URLs and directs them to the REST API. The plugin also provides Javascript access to the SAFE Network REST API. The SafeApp Web Framework sits on top of the plugin Javascript API, and the app itself on top of this, as shown in the [SAFEpress Web App Architecture diagram.](https://github.com/theWebalyst/safepress/blob/master/design/diagrams/safe-web-app-architecture.png)


### Operations:
SAFEpress will take care of operations by providing a JavaScript UI with different capabilities depending on the privileges of the "logged in" user. So "owner" can modify anything, "author" can create and submit a post for consideration, and "visitor" can post a comment for consideration. Only the owner (or administrators) can review and publish content, which they do by responding to "requests" (for publication, update or delete) received from authors, and visitors. 

I envisage the creator of a document will own the document, and can send a "request" for publication (update or delete), but will not be able to modify the public website directly.
When the owner or an administrator accepts a request, a copy of the new or changed document will be taken and stored, and the indexes updated to reflect the change. Only at this point will a change become public.
This two stage process ensures that no end user can break the internal data structures (using an outdated or sabotaged SAFEpress core). It assumes that the owner and any administrators are trusted in this respect. 

In practice any corruption is recoverable by the owner through roll back using the built in versioning of SAFE network storage. So we could provide the owner with the option to allow changes to be put through without review and authorisation, for example if they trust "subscribers" adequately, their comments could be made to appear directly upon submission. If so we would need to attempt to limit the damage that this could do if abused - so that only comments would be affected for example, or only comments on a single post - by limiting the data structures that a non-administrator is able to modify to the minimum required in order to support this.

### Data:
Document types will be things like page, post, comment, user-profile, plugins, widgets etc, each with its own metadata and content (which will include HTML/CSS & Javascript plugins and widgets).

An index will be maintained of all documents, with separate indexes for certain document types (such as page, post, user that need to be accessed quickly in batches, rather than directly by an identifier).
In some cases documents may have subtypes, indicated by their metadata (such as for posts of different type, where the type can affect how it is rendered in HTML).

Since users are not normally permitted to modify the website itself, and instead send requests post changes, "Data" will fall into two categories "Owner Data" and "User Data".
Only the owner and appointed administrators can modify "Owner Data", which is what will be rendered as the public website. They can also read, copy but not modify or delete "User Data". If a user modifies "User Data" it will not affect the public website without explicit acceptance of the change (unless the owner has decided to permit this in the limited circumstances we allow - as noted above).

### Rendering:
Rendering in a browser is performed by client side HTML/CSS/JavaScript that is written, or customised and extended from a template (like a bare CouchApp).

Like a CouchApp, the SAFEpress HTML/CSS and javascript of the website will be stored in a database (via the SAFE REST API) and delivered on the web browser accessing a "safe:" URL. On loading the page URL, the HTML and javascript can make further "safe:" requests to obtain embedded files, and issue SAFE core API javascript calls to obtain documents and content, exactly as existing client-side scripted web apps would do, to effect the rendering of the data on the page.

I imagine we will provide skeleton HTML/CSS pages to render different document types either as a page or as elements within a page. These "skeleton" pages can be replaced or customised by a website developer to change the features, branding, and user interface of the website.

### CMS:
We can start with just providing fully custom written rendering code with basic CMS document creation and editing features, such as create, edit, update, publish, delete of pages and posts. 

In fact we might begin without providing any document creation or editing at all, instead supporting import of data from other tools, including of course existing websites and CMS databases (such as WordPress, Drupal etc.). Later we can add the CMS UI to allow direct creation and editing of content.

### Interactive Customisation:
Interactive design UI can also be added later, to enable the look and feel to be modified through the SAFEpress user interface, in much the same way WordPress has developed over time. This could for example support editing of themes, based on establishing a themes styling mechanism (e.g. a heirarchy for how CSS is applied to 1) the whole website, 2) pages, 3) blog posts etc).

The UI can also be made customisable by following the WordPress model. For example, to start menus might be hand coded in the customised HTML/CMS/Javascript. Later, SAFEpress could provide for menus to be editable data structures, and extend the SAFEpress core UI to allow these to be read and written. A standard menu editing UI can then be included in the SAFEpress skeleton UI, meaning that end users can alter menus without having to edit code.

### Data Charges, Ownership and Rewards:
The website owner will own and pay for PUT of all Owner Data, and authors/user's will own and pay for PUT of User Data, and for sending requests to the owner/administrators.

GET rewards will only be significant for Owner Data, and by default would accrue to the owner, although we might by default apply author/user wallet addresses to the Owner Data copy of author/user submitted content. This would be in the interests of owners because it encourages authors and users to submit content and comments, though the owner will have the final say because they control everything on their public website.

### Shared Ownership:
It would be useful if multi-sig support can be applied to the CMS in ways that would allow joint ownership and control. To ensure this can't be subverted, it would need to be applied in a way that could not be circumvented - perhaps it would need to apply to the whole of the Core Data, ie the whole website or none of it. Needs to be thought through. Sharing of Owner Data GET rewards should also be considered.
