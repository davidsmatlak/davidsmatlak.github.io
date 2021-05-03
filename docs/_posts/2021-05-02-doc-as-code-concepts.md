---
layout: post
title:  Docs-as-code concepts
author: David Smatlak
permalink: docs-as-code-concepts
comments: false
description: This article explains docs-as-code concepts that are used for documentation.
---

_Docs-as-code_ is a framework to create documentation using workflows and tools that are used in
software development. To write code, software developers use text editors, source control,
programming languages, and do code reviews before a product is released. In docs-as-code, you apply
those same principles to your documentation development.

A goal in the docs-as-code model is to improve collaboration between writers and software
developers. It's easier to collaborate when similar processes and applications are used to create
documentation and code. These commonalities between teams help people focus on problem solving and
innovation as they work together.

Consider the following topics as you decide whether docs-as-code is correct for your organization.

## Applications

When writers and developers use similar applications, it reduces time spent to learn the other
team's specialized applications. For documentation, a text editor should integrate with source
control, command-line shell, and have extensions for markup languages. Text editors enhance file
sharing between teams because they can open files written with Markdown, Python, JavaScript, and
other file types. [Visual Studio Code](https://code.visualstudio.com) and [Atom](https://atom.io)
are examples of text editors used in docs-as-code.

Markdown is a plain-text markup language that's used to create documentation and format comments on
GitHub and GitLab. There are many flavors of Markdown, but the syntax is similar, such as [CommonMark](https://commonmark.org/help)
or [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown).

## Source control

Source control tracks changes to files in a repository. You'll have a local version that you use to
make changes and a cloud version that's used to publish your documentation. Source control always
has a main branch that contains the single source of truth. Writers and developers create
development branches to propose changes, but must request to have their changes merged into the main
branch.

[Git](https://git-scm.com) is a powerful source control system that's run from a command-line
prompt. [GitHub](https://github.com) and [GitLab](https://about.gitlab.com) are popular services
that host the source code repositories for software and documentation.

## Issue tracking and reviews

GitHub and GitLab have robust issue tracking systems that can be used by code or documentation
teams. Open source projects allow community contributions to submit change requests or to report
problems. When a problem is fixed or a new feature is added, use a review process to verify the
results. Reviewers can include writers, software developers, product managers, or community members.
After the change is verified, it's merged into the main repository. The documentation is published
the next time your site is built.

An _issue_ is used to describe a documentation problem or submit a change request. People
collaborate on the issue and communicate using comments that are tracked in chronological order.

A _pull request_ is submitted and reviewed to have changes that fix a problem or add new content to
an article merged into the main branch. During the review, everyone involved can view the changes
and use comments that are tracked in the same way as issues. You can link a pull request to an
existing issue which is helpful when you search for information about the changes.

## Automation

Automation can be basic or advanced and the level is dependent upon whether your organization has
engineers who can build and maintain the automation systems. Updated documentation is published when
your automation system runs a build of the updated source files.

For example, Markdown code can be rendered into HTML by a service such as [GitHub Pages](https://pages.github.com)
that uses the [Jekyll](https://jekyllrb.com) static site generator. There are other services
available that can monitor changes to a repository and automate publication, such as [Read the Docs](https://readthedocs.org)
or [Netlify](https://www.netlify.com/products/build). In general, when the main repository is
updated, the automation triggers a build to publish updated documentation.

For large organizations, using an engineered system can allow for customized extensions to render
content in a format that meets an organization's needs. The automated build system might even be
capable to publish documentation on a specified schedule with little or no human intervention.
