# ❄️ Setup Winter Action

This GitHub Action sets up a PHP environment suitable for running [Winter](https://github.com/wintercms/winter) and a fresh Winter installation. This is useful for running automated tests or checks for Winter plugins.

The Action works by doing the following:

- Checks out the [Winter repository](https://github.com/wintercms/winter) in the current working directory. By default, this will be the current development version, but can be configured to a version or branch of your choosing.
- Optionally, checks out the current repository as a plugin.
- Sets up a PHP environment using the [Setup PHP](https://github.com/shivammathur/setup-php) GitHub Action.
- Downloads all necessary Composer dependencies.

## Usage

You may include this as a step in your own GitHub Action workflow file, by using the following step definition:

```yaml
- name: Setup Winter
  uses: wintercms/setup-winter-action@v1
```

This should be placed under the `steps` definition of a job within your workflow.

The Setup Winter action has properties that may be used to configure how the environment is set up. These should be included under the `with` definition for the step:

Property | Default | Description
-------- | ------- | -----------
`php-version` | `'8.4'` | Defines the PHP version to install using the [Setup PHP](https://github.com/shivammathur/setup-php) GitHub Action.
`winter-ref` | `'develop'` | Defines the Winter version to install. This is a "ref" based on the [Winter repository](https://github.com/wintercms/winter), so you may use either a version such as `v1.2.7` or a branch name such as `develop`.
`plugin-name` and `plugin-author` | None | When both of these properties are defined, the current repository running the action will be checked out in the `plugins` directory using these values as the plugin location. For example, using `winter` as the `plugin-author` and `location` as the `plugin-name` will check out the current repository to `plugins/winter/location`. In addition, the Composer merge plugin will be configured to also include dependencies from your plugin, if a `composer.json` exists in your plugin.
`configure-winter` | None | If `true`, the Winter installation will also go through configuration steps into a working state. This is useful if you want to quickly *use* any Winter functionality in your CI tasks. This will run the Artisan commands `winter:env`, `key:generate` and sets up a temporary SQLite database and runs the migrations.
`admin-password` | `'admin'` | If you configure Winter, this will define the password for the default `admin` account. If this installation will be accessible in any way to an external system or the Internet, it is strongly recommended that you define a password using a secret.

For example, to set up your current repository as a plugin within Winter and test it against PHP 8.3, you could do the following:

```yaml
- name: Setup Winter
  uses: wintercms/setup-winter-action@v1
  with:
    php-version: '8.3'
    plugin-author: 'myauthor'
    plugin-name: 'myplugin'
```

By default, no configuration of Winter is done to allow you to define and conduct your own configuration in your automated tasks. If you want to quickly spool a working version of Winter, with a temporary SQLite database and admin account, you can use the `configure-winter` and `admin-password` properties:

```yaml
- name: Setup Winter
  uses: wintercms/setup-winter-action@v1
  with:
    php-version: '8.3'
    plugin-author: 'myauthor'
    plugin-name: 'myplugin'
    configure-winter: true
    admin-password: '${{ secrets.MY_ADMIN_PASSWORD }}'
```
