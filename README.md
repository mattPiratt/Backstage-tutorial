# A library including components everywhere

The smb-alice package delivers the complete Frontend-Stack to our assets. So we can develop and share scripts, templates, components and functionalities across our assets with ease.

- **Developing:** Alice makes it painless to create components base on twig, bootstrap and requireJS.
- **Centralized components:** The components is distributed via composer on our assets and can be included easily.
- **Customization:** Alice delivers components with default styling, but it's possible to customize the look and feel of each components with SCSS on the assets.

## 🧑‍💻 Development

Install composer packages via `composer install`

Install node packages via `npm ci`

To see your local changes e.g. on components you can [use sync task](#use-sync-task) to sync your local Alice project to another project to test it there or [combine Alice with smb-design-system](#combine-alice-with-smb-design-system).

### Use sync task

Therefore you have to duplicate [rsync-conf-example.json](https://github.com/smb-ag/smb-alice/blob/master/rsync-conf-example.json) and rename it to `rsync-conf.json`. In this file you can specify to which target location Alice should be synced. You can define multiple targets.

Example for rsync to giga SB on hn45 for developer kau:

```json
{
  "targets": [
    "kau@hn45.smbrands.de:~/repos_gigabob_1/vendor/smb/alice"
  ]
}
```

After that you can start a sync once with `npm run sync` or start the watcher `npm run watchSync` to sync whenever you change a file.

### Combine Alice with smb-design-system

Therefore you have to clone [smb-design-system](https://github.com/smb-ag/smb-design-system) from github and execute the steps to run smb-design-systems development environment. Then you clone Alice into the smb-design-system/vendor/smb/alice folder. If you work in Alice now you will see developed components on your local url of smb-design-system.

## Running tests

To run the unit tests (PHP and JS), hit this command in the root folder of the project

```console
npm run test:php
```

```console
npm run test:js
```

To update the snapshots run this command

```console
npm run test-update-snapshot:js
```

## Asset integration

The main purpose of this package is to share our components across our assets.

### composer

Use composer to install Alice on the asset by running this command in the folder where composer.json is located

```console
composer require smb/alice
```

Update the package with this command

```console
composer update smb/alice
```

### SCSS

In order to have the default styling of the components, you have to import the ``main-desktop.scss`` and a ``main-responsive.scss`` files from Alice into the asset. In the ``main-desktop.scss`` file are all breakpoints equal 0px. In the ``main-responsive.scss`` file, we have currently 3 breakpoints defined:

- $screen-xs: 480px
- $screen-md: 768px
- $screen-lg: 1000px

If you want to overwrite some bootstrap variables in Alice, you need to import the Alice variables file first, and the variables file of the asset thereafter.

**Example**

```sass
// Alice bootstrap variables
@import "/composer/vendor/smb/alice/core/assets/css/bootstrap/v3.3.7/variables";

// Bootstrap custom variables
@import "bootstrap/custom/variables";

// Alice core SCSS for desktop
@import "/composer/vendor/smb/alice/core/assets/css/main-desktop";

// OR

// Alice core SCSS for mobile
@import "/composer/vendor/smb/alice/core/assets/css/main-responsive";
```

### gulp

Some components have javascript functionalities, which is provided by Alice as requireJS modules. So in order to use these modules on the asset, you need to copy the JS folder from Alice into the asset. By extending the copy task of [smb-gulp](https://github.com/smb-ag/smb-gulp), it could look like this:

```js
smbgulp.setPaths({
  aliceFolder: 'composer/vendor/smb/alice',
  build: 'static-local/build',
  jsFolder: 'assets/js',
})

smbgulp.setConfig({
  copy: {
      src: path`{{aliceFolder}}/core/assets/js/**/*`,
      dest: path`{{build}}/{{jsFolder}}/vendor/alice`,
    }
  },
})
```

### requireJS

Now, if the Alice requireJS modules are copied into the asset, you need to registered them in the require config of the asset. The modules are located in a folder called ```alice-modules/```. Registering an Alice modules could look like this:

```js
require.config({
  paths: {
    // smb-alice modules
    'alice/navigation': 'assets/js/alice-modules/navigation',
  },
})

```

## Usage

The components are written in Twig and you can include them on the asset by using [namespacing](https://twig.symfony.com/doc/2.x/api.html#built-in-loaders). Use the following twig command to include a component easily

```twig
{% include '@alice_component/COMPONENT_NAME/COMPONENT_NAME.twig' with PROPERTIES %}
```

The ``PROPERTIES`` for each components are documented [here](/components/).

**Example**

```twig
{% include "@alice_component/teaser-itemstream/teaser-itemstream.twig" with {
  image: {
    ratio: '3by1',
    src_attr: item.images[0],
  },
  image_count: item.imageCount,
  caption: item.post.title,
  url: item.post.link,
  } only
%}
```

### Layout

Layout in Alice provides some markup based on bootstrap grid layout and Twig blocks as placeholder for content to be injected by the asset. Use this command to include a layout

```twig
{% include '@alice_layout/LAYOUT_NAME/LAYOUT_NAME.twig' with PROPERTIES only %}
```

## Scaffolding

Extending the Alice ```base.twig``` file to bootstrap the asset. Twig blocks are provided in the ```base.twig``` as placeholder, so you just need to modify these blocks for custom behaviour. Use ```{{ parent() }}``` to extend a block, without ```{{ parent() }}``` the block will be overwritten completely. For examble:


```twig
{% extends '@alice/base.twig' %}

{% block base_body %}
  <h1>Hello World!</h1>
{% endblock %}
```

