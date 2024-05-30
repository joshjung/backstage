---
id: faqs
title: TechDocs FAQ
sidebar_label: FAQ
description: This page answers frequently asked questions about TechDocs
---

This page answers frequently asked questions about [TechDocs](README.md).

## Questions

- [Why do you have separate "basic" and "recommended" deployment approaches?](#why-do-you-have-separate-basic-and-recommended-deployment-approaches)
- [Why don't you recommend techdocs-backend local filesystem to serve static files?](#why-dont-you-recommend-techdocs-backend-local-filesystem-to-serve-static-files)
- [Why aren't docs built on the fly i.e. when users visits a page, generate docs site in real-time?](#why-arent-docs-built-on-the-fly-ie-when-users-visits-a-page-generate-docs-site-in-real-time)
- [Can you use the techdocs plugin without the techdocs-backend plugin?](#can-you-use-the-techdocs-plugin-without-the-techdocs-backend-plugin)
- [What static site generator is TechDocs using?](#what-static-site-generator-is-techdocs-using)
- [What is the mkdocs-techdocs-core plugin?](#what-is-the-mkdocs-techdocs-core-plugin)
- [Does TechDocs support file formats other than Markdown (e.g. RST, AsciiDoc)?](#does-techdocs-support-file-formats-other-than-markdown-eg-rst-asciidoc-)

#### Why do you have separate "basic" and "recommended" deployment approaches?

The basic or out-of-the-box setup is what you get when you create a new app using `npx` or do a `git clone` of the
Backstage repository. We want the first experience to _just work_ so that you can experience TechDocs with little
effort. However, if you decide to deploy TechDocs for production use, the basic setup would work but there are going to
be downsides as you scale the number of documentation pages. So you would want to make sure the deployment is as
scalable as possible and therefore we recommend using external storage with caching, etc.

#### Why don't you recommend techdocs-backend local filesystem to serve static files?

It makes scaling a Backstage instance harder. Think about the case where we have distributed Backstage deployments
(e.g. multiple Kubernetes pods of your Backstage app). Using a separate/central file storage system for TechDocs is
necessary to make sure sites are persistent when the servers/pods are restarted and avoid duplicating sites per
instance. By having an external storage, we are making it easier to do some operations like delete a docs site or wipe
its contents.

#### Why aren't docs built on the fly i.e. when users visits a page, generate docs site in real-time?

Generating the content from Markdown on the fly is not optimal. Storage solutions act as a cache for the generated
static content. TechDocs is also currently built on MkDocs which does not allow us to generate docs per-page, so we
would have to build all docs for a entity on every request.

#### Can you use the techdocs plugin without the techdocs-backend plugin?

`techdocs` and `techdocs-backend` plugins are designed to be used together, like any other Backstage plugin with a
frontend and its backend (catalog, scaffolder, etc.). If you set your Backstage instance to generate docs on the server,
`techdocs-backend` will be responsible for managing the whole build process, making sure it's scalable. It is
responsible for securely communicating with the cloud storage provider, for both fetching static generated sites and
publishing the updates. There are other planned features like an authentication layer for users to determine whether
they have the permission to view a particular docs site. There are a handful of features which are extremely hard to
develop without a tightly integrated backend in place. Hence, support for `techdocs` without `techdocs-backend` is
limited and challenging to develop.

#### What static site generator is TechDocs using?

TechDocs is using [MkDocs](https://www.mkdocs.org/) to build project
documentation under the hood. Documentation built with the
[techdocs-container](https://github.com/backstage/techdocs-container) is using
the MkDocs [Material Theme](https://github.com/squidfunk/mkdocs-material).

#### What is the mkdocs-techdocs-core plugin?

The [mkdocs-techdocs-core](https://github.com/backstage/mkdocs-techdocs-core)
package is a MkDocs Plugin that works like a wrapper around multiple MkDocs
plugins (e.g.
[MkDocs Monorepo Plugin](https://github.com/spotify/mkdocs-monorepo-plugin)) as
well as a selection of Python Markdown extensions that TechDocs supports.

#### Does TechDocs support file formats other than Markdown (e.g. RST, AsciiDoc) ?

Not right now. We are currently using MkDocs to generate the documentation from
source, so the files have to be in Markdown format. However, in the future we
want to support other static site generators which will make it possible to use
other file formats.

#### What should be the value of `backstage.io/techdocs-ref` when using external build and storage?

The value of
[`backstage.io/techdocs-ref`](../software-catalog/well-known-annotations.md#backstageiotechdocs-ref)
metadata annotation is used in the build process of TechDocs. But when
[`techdocs.builder`](./configuration.md) is set to `'external'` in
`app-config.yaml`, the value of the annotation remains unused. However the
annotation should still be present in entity descriptor file (e.g.
`catalog-info.yaml`) for Backstage to know that TechDocs is enabled for the
entity.

#### Is it possible for users to suggest changes or provide feedback on a TechDocs page?

This is supported for TechDocs sites whose source code is hosted in either
GitHub or GitLab. In order to add "edit this page" and "leave feedback" buttons
on a TechDocs page, be sure that you have `repo_url` and `edit_uri` values in
your `mkdocs.yml` files per
[MkDocs instructions](https://www.mkdocs.org/user-guide/configuration).

If the host name of your source code hosting URL does not include `github` or
`gitlab`, an `integrations` entry in your `app-config.yaml` pointed at your
source code provider is also needed (only the `host` key is necessary).
