---
title: Using Hexo Blog Framework
tags:
---

## Creating a Draft
```
hexo new draft <blob post name in quotes>
```

```
hexo server --draft
```

```
hexo publish <ex: Deploying-Golang-API (i think it is case sensitive)>
```

`hexo generate`

```
hexo deploy
```
admin gui: 
`npm install --save hexo-admin`
then `hexo server -d` and open /admin

Create invalidation:
- in aws, go to cloudfront
- click on your bucket
- go to invalidations
- create invalidation with a path of `/*`

My need to remove cookies.

## in general flow is:
- create draft : `hexo new draft "<post name>"`
- publish draft `hexo publish <post name>`
- generate `hexo generate`
- deploy `hexo deploy`
- go to aws console and create invalidation