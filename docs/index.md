# Demo MKDocs Site

This site is built with the intention of determining the architecture of a documentation site with multiple source repositories. 

## Git Submodule Use

### Overview
This demo site takes advantage of Git Submodules to consolidate documentation under one "monolith" documentation site. 

This is made possible by [mkdocs-monorepo-plugin](https://github.com/backstage/mkdocs-monorepo-plugin). The plugin allows for the nesting of multiple mkdocs sites under one "big" site.

* Building out individual mkdocs sites for each repository gives us the flexibility of hosting them under the monorepo site, as demonstrated here, *or* as their own sites. 
    * My current recommendation is to host under the monorepo site. This will consolidate all of our documentation into one domain. However, if we see performance issues down the line with features such as search, we can always piviot back to hosting sites individually.
* The only cavoet with this is plugins from submodule sites don't propogate up to the main site. To ensure a plugin is used in the main site, it will need to be added to this repositories `mkdocs.yml` file.
    * This could be accomplished with some custom tooling, or just a manual addition to the file. It seems like plugins aren't updated too often, so this likely won't be a big point of friction.

### Integration
Integration of submodules introduces the question: How do we ensure the site stays up to date when a submodule's documentation content is changed? 

* Git Submodules reference individual commits. 

## Deploying the Site
Deployment of the site should occur on two conditions:

1. The main/deployment branch of the documentation site is updated.
2. A submodule's documentation is updated.

This would result in two GitHub actions, one to deploy the site when the main branch is updated, one to update submodules.

### Updating Submodules
Git Submodules reference individual commits. Because of this, we need to have some sort of automation to ensure we are keeping this documentation up to date. My proposed solution is Git Webhooks.

There are many types of webhooks, however what we'd want to use is a [Repository Webhook](https://docs.github.com/en/webhooks/types-of-webhooks#repository-webhooks)

For this, we configure the submodule repo (in this example DemoGraphQlDocumentation) using the following steps:

1. Navigate to DemoGraphQlDocumentation -> Settings -> Webhooks
2. Create a new Webhook.
3. Configure payload to be the main documentations dispatch url, example being `https://api.github.com/repos/cadereynoldson/demodocumentation/dispatches`
    * Be sure to configure this to use content-type `application/json`
4. Set the webhook to be triggered on push events. 
