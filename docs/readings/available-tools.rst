.. _available_tools:

***************
Available tools
***************

Each PHP container version brings the same tools, so you can safely switch versions without having
to worry to have less or more tools available.

.. seealso:: :ref:`tutorial_work_inside_the_php_container`

The PHP container is your workhorse and these are your tools:

+----------------------+-----------------------------------------------------------------------------------+
| Binary               | Tool                                                                              |
+======================+===================================================================================+
| various binaries     | `awesome-ci <https://github.com/cytopia/awesome-ci>`_                             |
+----------------------+-----------------------------------------------------------------------------------+
| ``brew``             | `Linux brew <http://linuxbrew.sh>`_                                               |
+----------------------+-----------------------------------------------------------------------------------+
| ``composer``         | `Composer <https://getcomposer.org>`_                                             |
+----------------------+-----------------------------------------------------------------------------------+
| ``drush``            | `Drush <http://www.drush.org>`_                                                   |
+----------------------+-----------------------------------------------------------------------------------+
| ``drupal``           | `Drupal Console <https://drupalconsole.com>`_                                     |
+----------------------+-----------------------------------------------------------------------------------+
| ``eslint``           | `ESLint <https://eslint.org>`_                                                    |
+----------------------+-----------------------------------------------------------------------------------+
| ``git``              | `Git <https://git-scm.com>`_                                                      |
+----------------------+-----------------------------------------------------------------------------------+
| ``git-flow``         | `Git flow <https://github.com/nvie/gitflow>`_                                     |
+----------------------+-----------------------------------------------------------------------------------+
| ``gulp``             | `Gulp <https://gulpjs.com/>`_                                                     |
+----------------------+-----------------------------------------------------------------------------------+
| ``grunt``            | `Grunt <https://gruntjs.com>`_                                                    |
+----------------------+-----------------------------------------------------------------------------------+
| ``jsonlint``         | `JSON lint <https://github.com/zaach/jsonlint>`_                                  |
+----------------------+-----------------------------------------------------------------------------------+
| ``laravel``          | `Laravel installer <https://github.com/laravel/installer>`_                       |
+----------------------+-----------------------------------------------------------------------------------+
| ``mdl``              | `Markdown lint <https://github.com/markdownlint/markdownlint>`_                   |
+----------------------+-----------------------------------------------------------------------------------+
| ``mdlint``           | `MD Linter <https://github.com/ChrisWren/mdlint>`_                                |
+----------------------+-----------------------------------------------------------------------------------+
| ``mongo*``           | Various MongoDB client tools                                                      |
+----------------------+-----------------------------------------------------------------------------------+
| ``mysql*``           | Various MySQL client tools                                                        |
+----------------------+-----------------------------------------------------------------------------------+
| ``mysqldump-secure`` | `mysqldump-secure <https://mysqldump-secure.org>`_                                |
+----------------------+-----------------------------------------------------------------------------------+
| ``node``             | `Node <https://nodejs.org>`_                                                      |
+----------------------+-----------------------------------------------------------------------------------+
| ``npm``              | `NPM <https://www.npmjs.com>`_                                                    |
+----------------------+-----------------------------------------------------------------------------------+
| ``pg*``              | Various PostgreSQL client tools                                                   |
+----------------------+-----------------------------------------------------------------------------------+
| ``phalcon``          | `Phalcon DevTools <https://github.com/phalcon/phalcon-devtools>`_                 |
+----------------------+-----------------------------------------------------------------------------------+
| ``phpcs``            | `PHP CodeSniffer <https://github.com/squizlabs/PHP_CodeSniffer>`_                 |
+----------------------+-----------------------------------------------------------------------------------+
| ``phpcbf``           | `PHP Code Beautifier and Fixer <https://github.com/squizlabs/PHP_CodeSniffer>`_   |
+----------------------+-----------------------------------------------------------------------------------+
| ``redis*``           | Various Redis client tools                                                        |
+----------------------+-----------------------------------------------------------------------------------+
| ``sass``             | `Sass <http://sass-lang.com>`_                                                    |
+----------------------+-----------------------------------------------------------------------------------+
| ``scss-lint``        | `SCSS Lint <https://github.com/brigade/scss-lint>`_                               |
+----------------------+-----------------------------------------------------------------------------------+
| ``symfony``          | `Symfony installer <https://github.com/symfony/symfony-installer>`_               |
+----------------------+-----------------------------------------------------------------------------------+
| ``tig``              | `Text-mode Interface for Git <https://github.com/jonas/tig>`_                     |
+----------------------+-----------------------------------------------------------------------------------+
| ``webpack``          | `Webpack <https://webpack.js.org>`_                                               |
+----------------------+-----------------------------------------------------------------------------------+
| ``wp``               | `Wordpress CLI <https://wp-cli.org>`_                                             |
+----------------------+-----------------------------------------------------------------------------------+
| ``yamllint``         | `Yamllint <https://github.com/adrienverge/yamllint>`_                             |
+----------------------+-----------------------------------------------------------------------------------+
| ``yarn``             | `Yarn <https://yarnpkg.com/en>`_                                                  |
+----------------------+-----------------------------------------------------------------------------------+

.. note::
    If you are in need of other tools, open up an issue at
    `Github <https://github.com/cytopia/devilbox/issues>`_ and ask for it,
    this can usually be implemented very quickly.

.. seealso::
    If you ever feel those tools are out-dated, simply update your Docker images.
    Docker images are built every night to ensure latest tools and security patches:
    :ref:`getting_started_update_the_docker_images`