---
date: 2020-03-09T22:03:48Z
title: "ERDs with MySQL + Schemaspy"
description: "Generating ERDs with MySQL + Schemaspy"
tags: ["docker", "mysql", "schemaspy"]
categories: []
externalLink: ""
series: []
---

I've recently had the dubious honour of building out a few greenfield projects rather than the more legacy software I'm used to dealing with.

One of the pros of this is that I've been able to have a properly normalised database from the start which made me wonder whether it was possible to do
some automatic self documenting with some ERDs.

Although MySQL Workbench (I think) will show you an ERD, it always feels to me something that should be made clear to you without having to use special
tooling. That combined with the fact we tend to use [TablePlus](https://www.tableplus.io) to connect to databases (consider it the database agnostic successor
to SequelPro), means another method is needed.

## Generate it
```
docker run --network myproject_default -v "$PWD/docs/schemaspy:/output/" schemaspy/schemaspy -t mysql -host mysql -db myproject_database -u root -p password -s myproject_database
```

This command is making the assumptions that:
1. you're running your local project through `docker-compose` under the name `myproject`
2. your database is called `myproject_database`

This will dump a bunch of generated schema docs into a folder called `docs` in your root project folder.

## Prevent Git saving it all

You only really want the ER diagram from all the stuff that it'll generate, so ideally you'll want to add this to your `.gitignore`

```
docs/*
!docs/schemaspy
docs/schemaspy/*
!docs/schemaspy/diagrams/
docs/schemaspy/diagrams/*
!docs/schemaspy/diagrams/summary/
docs/schemaspy/diagrams/summary/*
!docs/schemaspy/diagrams/summary/relationships.real.large.png
```

Unfortunately the `.gitignore` for dealing with nested directories like this is pretty verbose, but it works well enough.


## Make it visible!

Add something like this into your README.md:

`![db schema](docs/schemaspy/diagrams/summary/relationships.real.large.png)`

## Celebrate!

Your team now should hopefully have much better visiblility over what your tables are actually doing!

Hopefully your decisions up to this point have been good enough that they're now happy to now understand your well thought out design rather than
to have clear evidence of it being exactly as much nonsense as they suspected :P

## Example of TableSchema generated images

Pulled from the [Scribetools Documentation](https://scribetools.readthedocs.io/en/latest/_images/SchemaSpySchema.png)

![DB Schema](/posts/table-schema.png)
