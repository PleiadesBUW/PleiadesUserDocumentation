# PLEIADES Github Pages Docs

Hosted at https://pleiadesbuw.github.io/PleiadesUserDocumentation/ .
Custom domains can be configured like this: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages

Based on this theme: https://github.com/pages-themes/minimal -- useful to know to change certain configs etc.

## Adding a New Page
Create a markdown file, with some frontmatter:

```markdown
---
title: "Title of the page"
---

### Content
Yay, this is fun!
```

Add the page, e.g. `mynewpage.md`, to the navigation bar by adding the following to `_data/navigation.yml`:
```yaml
- title: mynewpage
  url: /mynewpage
```

You can also group pages by:
```yaml
- title: Group of things
  url: /things
  sublinks:
    - title: Sub-item 1
      url: /things/item1
```
But don't forget to create a file `/things.md` and a folder with files `things/any.md`, such that the navbar links lead to valid targets.


# Original README of Github Pages
## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/stderr-enst/PleiadesDocsTest/edit/main/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/stderr-enst/PleiadesDocsTest/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
