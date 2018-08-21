# The xGraph Wiki
## By Introspective Systems

### Updating Theme

This is the Introspective Systems' GitHub Pages repository, which serves as the xGraph wiki site. xGraph 
technical explanations and documentation live on this page. The xGraph wiki site was built using the Jekyll 
static site generator.

To update the xGraph wiki, first clone the [repository][repo]. 
Then you can set up the Jekyll theme to make changes locally using this [GitHub pages guide][guide].

[repo]: https://github.com/IntrospectiveSystems/introspectivesystems.github.io
[guide]: https://help.github.com/articles/adding-a-jekyll-theme-to-your-github-pages-site/#installing-your-theme-using-bundler

This page was built off the Minima Jekyll theme. The core files can be found in the Minima Gem file. See 
the [Jekyll themes documentation](https://jekyllrb.com/docs/themes/) for more information on building off a 
jekyll theme.

### Adding Pages

Jekyll makes it easy to add pages to any website by storing page content in markdown files. So you can 
update and add content to the xGraph wiki by updating the markdown files found in the `_pages` directory. 
Developers must add, remove and update pages and subpages as necessary to keep the pages up to date. For 
details on how to add pages to Jekyll sites, see the [Creating Pages guide](https://jekyllrb.com/docs/pages/).

Page content on the xGraph wiki is saved in the `_pages` folder. It is organized into root pages and subpages 
which are organized into folders. Jekyll loads the pages in order, so there is a numerical prefix that will 
determine the order the page is displayed. Jekyll pages enable the use of tags and catagories for 
organizing pages and posts, and we use tags to organize our pages and subpages. 

In the page name prefix, the first two digits denote the order the pages will be displayed, and the second 
digit denotes the order the subpages will be displayed. Root pages, which are displayed on the horizontal 
navigation bar, should be prefixed xx00. Subpages should then be ordered xx01, xx02, ... in the order that 
the pages will be displayed on the navigation sidebar.

Each page should be labeled with the appropriate tags. All root pages must have the tag `root` to be 
displayed on the horizontal navigation bar. Additionally, each page should be tagged with it's subsection. 
Any page with a subsection tag will be displayed on the navigation sidebar of the other subsection pages.

Documentation for the modules in the Introspective System's module broker live in a `module-broker-modules` 
directory in the `module-broker` subsection. These files do not have a numerical prefix and will be 
displayed alphabetically. This section should only be necessary until the module broker's online interface 
is developed. 