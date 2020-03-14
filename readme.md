
Welcome to the git repo for documentation for Nez.

## Local development

`gem install bundler` to get the ruby Bundler then `bundle install` to get the required gems. 

Use `./localdev.sh` or `sh localdev.sh` to start the jekyll server.

## Adding content

in `./src/` is where the site content is kept. Add a `x.md` with frontmatter into one of the folders
to make it an article under that header. If you want to make a new header, create a folder with an
`index.md` file with front matter from below.

## Front matter templates

### Container Frontmatter
```yml
---
layout: default
title: Item Title
nav_order: 1
has_children: true
---
```

### Article Frontmatter
```yml
---
layout: default
title: Item Title
nav_order: 1
parent: Parent Title
---
```