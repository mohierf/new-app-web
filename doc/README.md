# Project documentation

## Setup and development

Doc for the project setup and development how-to is located in [the _dev_ directory](dev/README.md).

[This file](dev/structure.md) documents the project organization and structure
 

## PHPStorm, docker, Sonar Qube

Configure PHPStorm to use Docker and SonarQube. [View this doc](tools/README.md).


## Tests

Doc for the application tests is located in [the _test_ directory](test/README.md).


## Theming

apTelecare comes with multiple themes presets defined in the */src/resources/_branding.js* file.

 Each theme requires the following elements :
 - a *manifest.json* file in **/src/resources/branding/themeName**
 - the application icons in **/src/resources/branding/themeName**
 - specific logos and stylesheets in project **/src/resources/branding/themeName/images** and **/src/resources/branding/themeName/sass/themeName.scss**
 - the webpack configuration in order to build all assets for production exists in **/webpack.config.js**
 
 A theme requires 3 mandatory pictures : white logotype, colored logotype, and HD background picture (at least 1600 px wide)
 It also requires the 3 following variables :
```sh
  themeName: {
    APP_NAME: 'Brand Name',
    APP_TITLE: 'Web page title',
    APP_EDITOR: 'Editor Name'
  },
```

**Note:** Each theme comes with custom email templates with coordinated colors and logos. These emails templates and associated logotypes are defined in the `api` project.

apTelecare is based on a custom Bootstrap based template. Base styles are defined in the **/src/resources/sass/** folder.
This folder contains the default apTelecare style and the components and global layout styles.

The SAAS stylesheets are compiled for production using the webpack configuration file.


# i18n / localization

apTelecare features internationalisation of dates thanks to moment-timezone library and texts thanks to react-intl.

User is allowed to switch dynamically between available languages defined in the **/src/shared-patient-lib/lang** folder.

Dates in the app are displayed based on the user timezone and its locale.

[This specific document](i18n/README.md) explains how to add a new language in the application.
