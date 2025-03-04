---
title: Gatsby Config API
---

The file `gatsby-config.js` defines your site's metadata, plugins, and other general configuration. This file should be in the root of your Gatsby site.

If you created a Gatsby site with the `gatsby new` command, there should already be a sample configuration file in your site's directory.
_Note: There are many sample configs which may be helpful to reference in the different [Gatsby Example Websites](https://github.com/gatsbyjs/gatsby/tree/master/examples)._

## Set up the configuration file

The configuration file should export a JavaScript object. Within this object, you can define several different configuration options.

```javascript:title=gatsby-config.js
module.exports = {
  //configuration object
}
```

An example `gatsby-config.js` file could look like this:

```javascript:title=gatsby-config.js
module.exports = {
  siteMetadata: {
    title: `Gatsby`,
  },
  plugins: [
    `gatsby-transform-plugin`,
    {
      resolve: `gatsby-plugin-name`,
      options: {
        optionA: true,
        optionB: `Another option`,
      },
    },
  ],
}
```

## Configuration options

Options available to set within `gatsby-config.js` include:

1. [siteMetadata](#sitemetadata) (object)
1. [plugins](#plugins) (array)
1. [flags](#flags) (object)
1. [pathPrefix](#pathprefix) (string)
1. [trailingSlash](#trailingslash) (string)
1. [polyfill](#polyfill) (boolean)
1. [mapping](#mapping-node-types) (object)
1. [proxy](#proxy) (object)
1. [developMiddleware](#advanced-proxying-with-developmiddleware) (function)
1. [jsxRuntime](#jsxruntime) (string)
1. [jsxImportSource](#jsximportsource) (string)

## siteMetadata

When you want to reuse common pieces of data across the site (for example, your site title), you can store that data in `siteMetadata`:

```javascript:title=gatsby-config.js
module.exports = {
  siteMetadata: {
    title: `Gatsby`,
    siteUrl: `https://www.gatsbyjs.com`,
    description: `Blazing fast modern site generator for React`,
  },
}
```

This way you can store it in one place, and pull it whenever you need it. If you ever need to update the info, you only have to change it here.

See a full description and sample usage in [Gatsby.js Tutorial Part Four](/docs/tutorial/part-4/#data-in-gatsby).

## plugins

Plugins are Node.js packages that implement Gatsby APIs. The config file accepts an array of plugins. Some plugins may need only to be listed by name, while others may take options (see the docs for individual plugins).

Installing a plugin using a package manager like `npm` **does not** enable it in your Gatsby site. To finish adding a plugin, make sure your `gatsby-config.js` file has a `plugins` array so you can include a space for the plugins needed to build your site:

```javascript:title=gatsby-config.js
module.exports = {
  plugins: [
    //plugins go here
  ],
}
```

When adding multiple plugins, they should be separated by commas in the `plugins` array to support valid JavaScript syntax.

### Plugins without options

If a plugin does not require any options, you can add its name as a string to the `plugins` array:

```javascript:title=gatsby-config.js
module.exports = {
  plugins: [`gatsby-plugin-name`],
}
```

### Plugins with options

Many plugins have optional or required options to configure them. Instead of adding a name string to the `plugins` array, add an object with its name and options. Most plugins show examples in their `README` file or page in the [Gatsby plugin library](/plugins).

Here's an example showing how to write an object with keys to `resolve` the plugin name and an `options` object with any applicable settings:

```javascript:title=gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-plugin-name`,
      options: {
        optionA: true,
        optionB: `Another option`,
      },
    },
  ],
}
```

### Mixed plugins

You can add plugins with and without options in the same array. Your site's config file could look like this:

```javascript:title=gatsby-config.js
module.exports = {
  plugins: [
    `gatsby-transform-plugin`,
    {
      resolve: `gatsby-plugin-name`,
      options: {
        optionA: true,
        optionB: `Another option`,
      },
    },
  ],
}
```

See more about [Plugins](/docs/plugins/) for more on utilizing plugins, and to see available official and community plugins.

## flags

Flags let sites enable experimental or upcoming changes that are still in testing or waiting for the next major release.

[Go here to see a list of the current flags](https://github.com/gatsbyjs/gatsby/blob/master/packages/gatsby/src/utils/flags.ts).

```javascript:title=gatsby-config.js
module.exports = {
  flags: {
    QUERY_ON_DEMAND: true,
  },
}
```

## pathPrefix

It's common for sites to be hosted somewhere other than the root of their domain. Say you have a Gatsby site at `example.com/blog/`. In this case, you would need a prefix (`/blog`) added to all paths on the site.

```javascript:title=gatsby-config.js
module.exports = {
  pathPrefix: `/blog`,
}
```

See more about [Adding a Path Prefix](/docs/how-to/previews-deploys-hosting/path-prefix/).

## trailingSlash

Configures the creation of URLs for pages, and whether to remove, append, or ignore trailing slashes.

- `always`: Always add trailing slashes to each URL, e.g. `/x` to `/x/`.
- `never`: Remove all trailing slashes on each URL, e.g. `/x/` to `/x`.
- `ignore`: Don't automatically modify the URL

The default setting for this option is `legacy` in order to preserve existing behavior for current users. In Gatsby v5 the default mode will be `always`. Gatsby Cloud automatically handles and supports the `trailingSlash` option. Alternate hosting providers (or if you're managing this on your own) should follow the "Redirects, and expected behavior from the hosting provider" section on the [initial RFC](https://github.com/gatsbyjs/gatsby/discussions/34205).

## polyfill

Gatsby uses the ES6 Promise API. Because some browsers don't support this, Gatsby includes a Promise polyfill by default.

If you'd like to provide your own Promise polyfill, you can set `polyfill` to false.

```javascript:title=gatsby-config.js
module.exports = {
  polyfill: false,
}
```

See more about [Browser Support](/docs/how-to/custom-configuration/browser-support/#polyfills) in Gatsby.

## Mapping node types

**Please note:** We strongly recommend using the [`@link` GraphQL directive](/docs/reference/graphql-data-layer/schema-customization/#foreign-key-fields) instead. It supports more use cases and will be the preferred method for foreign-keys in the future.

Gatsby includes a feature that lets you create "mappings" between node types.

For instance, imagine you have a multi-author markdown blog where you want to "link" from each blog post to the author information stored in a YAML file named `author.yaml`:

```markdown
---
title: A blog post
author: Kyle Mathews
---

A treatise on the efficacy of bezoar for treating agricultural pesticide poisoning.
```

```yaml:title=author.yaml
- name: Kyle Mathews
  bio: Founder @ GatsbyJS. Likes tech, reading/writing, founding things. Blogs at bricolage.io.
  twitter: "@kylemathews"
```

You can map between the `author` field in `frontmatter` to the `name` in the `author.yaml` objects by adding to your `gatsby-config.js`:

```javascript
module.exports = {
  plugins: [...],
  mapping: {
    "MarkdownRemark.frontmatter.author": `AuthorYaml.name`,
  },
}
```

You may need to install the appropriate file transformer (in this case [YAML](/plugins/gatsby-transformer-yaml/)) and set up [gatsby-source-filesystem](/plugins/gatsby-source-filesystem/) properly for Gatsby to pick up the mapping files. This applies to other file types later mentioned in this segment as well.

Gatsby then uses this mapping when creating the GraphQL schema to enable you to query data from both sources:

```graphql
query ($slug: String!) {
  markdownRemark(fields: { slug: { eq: $slug } }) {
    html
    fields {
      slug
    }
    frontmatter {
      title
      author {
        # This now links to the author object
        name
        bio
        twitter
      }
    }
  }
}
```

Mapping can also be used to map an array of ids to any other collection of data. For example, if you have two JSON files `experience.json` and `tech.json` as follows:

```json:title=experience.json
[
  {
    "id": "companyA",
    "company": "Company A",
    "position": "Unicorn Developer",
    "from": "Dec 2016",
    "to": "Present",
    "items": [
      {
        "label": "Responsibility",
        "description": "Being an unicorn"
      },
      {
        "label": "Hands on",
        "tech": ["REACT", "NODE"]
      }
    ]
  }
]
```

```json:title=tech.json
[
  {
    "name": "REACT",
    "icon": "facebook",
    "color": "teal",
    "label": "React"
  },
  {
    "name": "NODE",
    "icon": "server",
    "color": "green",
    "label": "NodeJS"
  }
]
```

And then add the following rule to your `gatsby-config.js`:

```javascript:title=gatsby-config.js
module.exports = {
  plugins: [...],
  mapping: {
    'ExperienceJson.items.tech': `TechJson.name`
  },
}
```

You can query the `tech` object via the referred items in `experience`:

```graphql
query {
  allExperienceJson {
    edges {
      node {
        company
        position
        from
        to
        items {
          label
          description
          link
          tech {
            label
            color
            icon
          }
        }
      }
    }
  }
}
```

Mapping also works between Markdown files. For example, instead of having all authors in a YAML file, you could have info about each author in a separate Markdown file:

```markdown
---
author_id: Kyle Mathews
twitter: "@kylemathews"
---

Founder @ GatsbyJS. Likes tech, reading/writing, founding things. Blogs at bricolage.io.
```

And then add the following rule to your `gatsby-config.js`:

```javascript:title=gatsby-config.js
module.exports = {
  plugins: [...],
  mapping: {
    'MarkdownRemark.frontmatter.author': `MarkdownRemark.frontmatter.author_id`
  },
}
```

Similarly to YAML and JSON files, mapping between Markdown files can also be used to map an array of ids.

## Proxy

Setting the proxy config option will tell the develop server to proxy any unknown requests to your specified server. For example:

```javascript:title=gatsby-config.js
module.exports = {
  proxy: {
    prefix: "/api",
    url: "http://examplesite.com/api/",
  },
}
```

See more about [Proxying API Requests in Develop](/docs/api-proxy/).

## Advanced proxying with `developMiddleware`

See more about [adding develop middleware](/docs/api-proxy/#advanced-proxying).

## jsxRuntime

Setting to "automatic" allows the use of JSX without having to import React. More information can be found on the [Introducing the new JSX Transform](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html) blog post.

```javascript:title=gatsby-config.js
module.exports = {
  jsxRuntime: "automatic",
}
```

## jsxImportSource

With the new jsxRuntime you can set which package React should use as underlying jsx transformer. For example you can set it to "@emotion/react" so by default @emotion/react is used instead of the react package.

```javascript:title=gatsby-config.js
module.exports = {
  jsxImportSource: "@emotion/react",
}
```
