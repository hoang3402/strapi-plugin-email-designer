# Strapi Email Designer plugin 💅

<p align="left">
  <a href="https://www.npmjs.org/package/strapi-plugin-email-designer">
    <img src="https://img.shields.io/npm/v/strapi-plugin-email-designer.svg?style=plastic" alt="NPM Version" /></a>
  <a href="https://www.npmjs.org/package/strapi-plugin-email-designer">
    <img src="https://img.shields.io/npm/dt/strapi-plugin-email-designer.svg?style=plastic" alt="Monthly download on NPM" /></a>
  <a href="https://github.com/prettier/prettier" target="_blank" rel="noopener noreferrer">
    <img alt="code style: prettier" src="https://img.shields.io/badge/code_style-prettier-ff69b4.svg?style=plastic"></a>
  <a href="#-contributing">
    <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=plastic" alt="PRs welcome!" /></a>
  <a href="#-license">
    <img src="https://img.shields.io/github/license/alexzaganelli/strapi-plugin-email-designer?style=plastic" alt="License" /></a>
  <a href="https://twitter.com/intent/follow?screen_name=alexzaganelli" target="_blank" rel="noopener noreferrer">
    <img alt="Follow Alex Zaganelli" src="https://img.shields.io/twitter/follow/alexzaganelli?color=%231DA1F2&label=follow%20me&style=plastic"></a>
  <a href="#">
    <img alt="Repo stars" src="https://img.shields.io/github/stars/alexzaganelli/strapi-plugin-email-designer?color=white&label=Github&style=plastic"></a>
  <!-- ALL-CONTRIBUTORS-BADGE:START - Do not remove or modify this section -->
<a href="#contributors-"><img alt="Contributors" src="https://img.shields.io/badge/all_contributors-14-orange.svg?style=plastic"></a>
<!-- ALL-CONTRIBUTORS-BADGE:END -->
</p>

Design your own email templates directly from the [Strapi CMS](https://github.com/strapi/strapi) admin panel and use the magic to send programmatically email from your controllers / services.

<img src="https://raw.githubusercontent.com/alexzaganelli/strapi-plugin-email-designer/main/public/assets/strapi-email-designer-v4.jpg" alt="Designer screenshot" />

_Visual composer provided by [Unlayer](https://unlayer.com/)_

&nbsp;

## ⚙️ Versions

- **Strapi v4** - (current) - [v2.x](https://github.com/alexzaganelli/strapi-plugin-email-designer)
- **Strapi v3** - [v1.x](https://github.com/alexzaganelli/strapi-plugin-email-designer/tree/strapi-v3)

&nbsp;

## ⏳ Installation

Install Strapi with this **Quickstart** command to create a Strapi project instantly:

- (Use **yarn** to install the Strapi project (recommended). [Install yarn with these docs](https://yarnpkg.com/lang/en/docs/install/).)

```bash
# with yarn
yarn create strapi-app my-project --quickstart

# with npm/npx
npx create-strapi-app my-project --quickstart
```

_This command generates a brand new project with the default features (authentication, permissions, content management, content type builder & file upload). The **Quickstart** command installs Strapi using a **SQLite** database which is used for prototyping in development._

- Configure the `Strapi email` plugin ( [official documentation](https://strapi.io/documentation/developer-docs/latest/plugins/email.html#configure-the-plugin) )

- Add the `strapi-designer` plugin

```bash
yarn add strapi-plugin-email-designer@latest

# or

npm i -S strapi-plugin-email-designer@latest
```

- you may need also to add to the unlayer domain to the _Content Security Policy_. Update the config file `config/middlewares.js` as:

```diff
// ...
- "strapi::security",
+ {
+     name: "strapi::security",
+     config: {
+       contentSecurityPolicy: {
+         directives: {
+           "script-src": ["'self'", "editor.unlayer.com", "editor.unlayer.com/embed.js"],
+           "frame-src": ["'self'", "editor.unlayer.com"],
+           "img-src": [
+             "'self'",
+             "data:",
+             "cdn.jsdelivr.net",
+             "strapi.io",
+             "s3.amazonaws.com",
+           ],
+         },
+       },
+     },
+   },
// ...
```

- After successful installation you've to build a fresh package that includes plugin UI. To archive that simply use:

```bash
yarn build && yarn develop

# or

npm run build && npm run develop
```

- or just run Strapi in the development mode with `--watch-admin` option:

```bash
yarn develop --watch-admin

#or

npm run develop --watch-admin
```

The **Email Designer** plugin should appear in the **Plugins** section of Strapi sidebar after you run app again.

## 💄 Usage

1. Design your template with easy on the visual composer. For variables use [{{mustache}} templating language](https://github.com/janl/mustache.js) with the double curly braces tags ( `{{` and `}}` ). **You can leave the text version blank to automatically generate a text version of your email from the HTML version.**

Tips: in the template's body is possible to iterate array like this:

```text
{{#order.products}}
    <li>{{name}}</li>
    <li>${{price}}</li>
{{/order.products}}
```

2. Send email programmatically:

```javascript
{
  // ...

  try {
    await strapi
      .plugin('email-designer')
      .service('email')
      .sendTemplatedEmail(
        {
          // required
          to: 'to@example.com',

          // optional if /config/plugins.js -> email.settings.defaultFrom is set
          from: 'from@example.com',

          // optional if /config/plugins.js -> email.settings.defaultReplyTo is set
          replyTo: 'reply@example.com',

          // optional array of files
          attachments: [],
        },
        {
          // required - Ref ID defined in the template designer (won't change on import)
          templateReferenceId: 9,

          // If provided here will override the template's subject.
          // Can include variables like `Thank you for your order {{= USER.firstName }}!`
          subject: `Thank you for your order`,
        },
        {
          // this object must include all variables you're using in your email template
          USER: {
            firstname: 'John',
            lastname: 'Doe',
          },
          order: {
            products: [
              { name: 'Article 1', price: 9.99 },
              { name: 'Article 2', price: 5.55 },
            ],
          },
          shippingCost: 5,
          total: 20.54,
        }
      );
  } catch (err) {
    strapi.log.debug('📺: ', err);
    return ctx.badRequest(null, err);
  }

  // ...
}
```

**Enjoy 🎉**

## 🖐 Requirements

Complete installation requirements are exact same as for Strapi itself and can be found in the documentation under <a href="https://strapi.io/documentation/v3.x/installation/cli.html#step-1-make-sure-requirements-are-met">Installation Requirements</a>.

**Supported Strapi versions**:

- Strapi v4.x.x

(This plugin may work with the older Strapi versions, but these are not tested nor officially supported at this time.)

**Node / NPM versions**:

- NodeJS >= 14.21 < 19
- NPM >= 7.x

**We recommend always using the latest version of Strapi to start your new projects**.

## 🔧 Configuration

You can pass configuration options directly to the editor that is used by this plugin. To do so, in your `config/plugins.js` file of your project, configure the plugin like this example:

```javascript
module.exports = ({ env }) => ({
  // ...
  'email-designer': {
    enabled: true,

    // ⬇︎ Add the config property
    config: {
      editor: {
        // optional - if you have a premium unlayer account
        projectId: [UNLAYER_PROJECT_ID],

        tools: {
          heading: {
            properties: {
              text: {
                value: 'This is the new default text!',
              },
            },
          },
        },
        options: {
          features: {
            colorPicker: {
              presets: ['#D9E3F0', '#F47373', '#697689', '#37D67A'],
            },
          },
          fonts: {
            showDefaultFonts: false,
            /*
             * If you want use a custom font you need a premium unlayer account and pass a projectId number :-(
             */
            customFonts: [
              {
                label: 'Anton',
                value: "'Anton', sans-serif",
                url: 'https://fonts.googleapis.com/css?family=Anton',
              },
              {
                label: 'Lato',
                value: "'Lato', Tahoma, Verdana, sans-serif",
                url: 'https://fonts.googleapis.com/css?family=Lato',
              },
              // ...
            ],
          },
          mergeTags: [
            {
              name: 'Email',
              value: '{{ USER.username }}',
              sample: 'john@doe.com',
            },
            // ...
          ],
        },
        appearance: {
          theme: 'dark',
          panels: {
            tools: {
              dock: 'left',
            },
          },
        },
      },
    },
  },
  // ...
});
```

See [Unlayer's documentation](https://docs.unlayer.com) for more options.

## 🚨 How to run the tests

Create the `cypress.env.json` file to the root and add your variables following this schema:

```json
{
  "adminUrl": "http://localhost:1337/admin/auth/login",
  "user": {
    "email": "john.doe@example.com",
    "password": "P1pp0#2021"
  }
}
```

Now let's install and open Cypress

```bash
# with yarn
yarn cypress:install
yarn cypress:open

# with npm
npm run cypress:install
npm run cypress:open

```

## 🚧 Roadmap

- [x] Template composer helper
- [x] Import design feature
- [x] Override Strapi's core email system feature
- [x] Preview email with real data feature
- [x] Tags feature
- [ ] Custom components extension
- [ ] Complete UI tests
- [ ] i18n translations (help wanted!)

## 🤝 Contributing

Feel free to fork and make a Pull Request to this plugin project. All the input is warmly welcome!

## ⭐️ Show your support

Give a star if this project helped you.

## 🔗 Links

- [NPM package](https://www.npmjs.com/package/strapi-plugin-email-designer)
- [GitHub repository](https://github.com/alexzaganelli/strapi-plugin-email-designer)

## 🌎 Community support

- For general help using Strapi, please refer to [the official Strapi documentation](https://docs.strapi.io/developer-docs/latest/getting-started/introduction.html).
- For support with this plugin you can DM me in the Strapi Discord [channel](https://discord.strapi.io/).
- You can DM me on [Twitter](https://twitter.com/alexzaganelli)

## 📝 License

[MIT License](LICENSE.md) Copyright (c) 2020 [Alex Zaganelli](https://alexzaganelli.com/) &amp; [Strapi Solutions](https://strapi.io/).

## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tbody>
    <tr>
      <td align="center" valign="top" width="14.28%"><a href="https://alexzaganelli.com"><img src="https://avatars.githubusercontent.com/u/1064582?v=4?s=100" width="100px;" alt="Alexandre Zaganelli"/><br /><sub><b>Alexandre Zaganelli</b></sub></a><br /><a href="#ideas-alexzaganelli" title="Ideas, Planning, & Feedback">🤔</a> <a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/commits?author=alexzaganelli" title="Code">💻</a> <a href="#design-alexzaganelli" title="Design">🎨</a> <a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/issues?q=author%3Aalexzaganelli" title="Bug reports">🐛</a></td>
      <td align="center" valign="top" width="14.28%"><a href="http://www.tuhaogo.com/"><img src="https://avatars.githubusercontent.com/u/1281294?v=4?s=100" width="100px;" alt="Ron Chi"/><br /><sub><b>Ron Chi</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/issues?q=author%3Abannerchi" title="Bug reports">🐛</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/pr0gr8mm3r"><img src="https://avatars.githubusercontent.com/u/37022952?v=4?s=100" width="100px;" alt="p_0g_8mm3_"/><br /><sub><b>p_0g_8mm3_</b></sub></a><br /><a href="#design-pr0gr8mm3r" title="Design">🎨</a> <a href="#ideas-pr0gr8mm3r" title="Ideas, Planning, & Feedback">🤔</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://tobias-thiele.de/"><img src="https://avatars.githubusercontent.com/u/13473174?v=4?s=100" width="100px;" alt="Tobias Thiele"/><br /><sub><b>Tobias Thiele</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/commits?author=creazy231" title="Code">💻</a> <a href="#design-creazy231" title="Design">🎨</a> <a href="#ideas-creazy231" title="Ideas, Planning, & Feedback">🤔</a></td>
      <td align="center" valign="top" width="14.28%"><a href="http://guilleangulo.me"><img src="https://avatars.githubusercontent.com/u/50624358?v=4?s=100" width="100px;" alt="Guillermo Angulo"/><br /><sub><b>Guillermo Angulo</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/issues?q=author%3AGuilleAngulo" title="Bug reports">🐛</a> <a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/commits?author=GuilleAngulo" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://www.linkedin.com/in/xaviercivit/"><img src="https://avatars.githubusercontent.com/u/54951764?v=4?s=100" width="100px;" alt="Xavier Civit"/><br /><sub><b>Xavier Civit</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/issues?q=author%3Axcivit" title="Bug reports">🐛</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/jpizzle34"><img src="https://avatars.githubusercontent.com/u/48883158?v=4?s=100" width="100px;" alt="jpizzle34"/><br /><sub><b>jpizzle34</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/commits?author=jpizzle34" title="Code">💻</a></td>
    </tr>
    <tr>
      <td align="center" valign="top" width="14.28%"><a href="https://www.linkedin.com/in/moritzeck"><img src="https://avatars.githubusercontent.com/u/22457755?v=4?s=100" width="100px;" alt="Moritz Eck"/><br /><sub><b>Moritz Eck</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/commits?author=meck93" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/B0rk3"><img src="https://avatars.githubusercontent.com/u/40357504?v=4?s=100" width="100px;" alt="B0rk3"/><br /><sub><b>B0rk3</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/commits?author=B0rk3" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://nihey.org"><img src="https://avatars.githubusercontent.com/u/5278570?v=4?s=100" width="100px;" alt="Nihey Takizawa"/><br /><sub><b>Nihey Takizawa</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/commits?author=nihey" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/ggirodda"><img src="https://avatars.githubusercontent.com/u/12408871?v=4?s=100" width="100px;" alt="Ciro Alabrese"/><br /><sub><b>Ciro Alabrese</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/commits?author=ggirodda" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://zaeck.ch/"><img src="https://avatars.githubusercontent.com/u/22471485?v=4?s=100" width="100px;" alt="Nik Zaugg"/><br /><sub><b>Nik Zaugg</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/commits?author=nikzaugg" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="http://www.paperlesswork.de"><img src="https://avatars.githubusercontent.com/u/35005267?v=4?s=100" width="100px;" alt="Pascal Oberbeck"/><br /><sub><b>Pascal Oberbeck</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/issues?q=author%3Apoberbeck" title="Bug reports">🐛</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/zguig52"><img src="https://avatars.githubusercontent.com/u/12774668?v=4?s=100" width="100px;" alt="Guillaume REMBERT"/><br /><sub><b>Guillaume REMBERT</b></sub></a><br /><a href="https://github.com/alexzaganelli/strapi-plugin-email-designer/commits?author=zguig52" title="Code">💻</a></td>
    </tr>
  </tbody>
</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!

## Stargazers ✨

[![Stargazers repo roster for @alexzaganelli/strapi-plugin-email-designer](https://reporoster.com/stars/alexzaganelli/strapi-plugin-email-designer)](https://github.com/alexzaganelli/strapi-plugin-email-designer/stargazers)

## Forkers ✨

[![Forkers repo roster for @alexzaganelli/strapi-plugin-email-designer](https://reporoster.com/forks/alexzaganelli/strapi-plugin-email-designer)](https://github.com/alexzaganelli/strapi-plugin-email-designer/network/members)

## Support Me ✨

If you like this plugin I'm very happy, so lets drink a beer. _Salute! 🍻_

[!["Buy Me A Beer"](https://img.buymeacoffee.com/button-api/?text=Buy+me+a+beer&emoji=🍺&slug=alexzaganelli&button_colour=FFDD00&font_colour=000000&font_family=Cookie&outline_colour=000000&coffee_colour=ffffff)](https://www.buymeacoffee.com/alexzaganelli)
