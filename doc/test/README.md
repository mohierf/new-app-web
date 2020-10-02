# Project documentation

## Tests fixtures

The `app-web` application has its own Symfony/Doctrine tests fixtures (directory _fixtures/_) that must be loaded in the backend for the e2e tests.


Start the application in test mode:
```sh
docker-compose -f docker-compose.yml -f docker-compose.override.yml -f docker-compose.test.yml up
```

```sh
# Database creation (run once)
docker-compose -f docker-compose.test.yml exec -T api-php bash -c 'gosu ${SYMFONY_USER} bin/console doctrine:schema:update --force'

# Database fixtures load
docker-compose -f docker-compose.test.yml exec -T api-php bash -c 'gosu ${SYMFONY_USER} bin/console hautelook:fixtures:load --no-interaction --purge-with-truncate'
```

You can also load the fixtures via an HTTP call:
 
```sh
curl -XGET "http://localhost/_test/database/reset"
```

Point your browser to *http://localhost:3001* to use the application with this username: `patient1@tmm-software.tech` and password: `Medic123`.
 

## Unit tests

__Jest__ is used as JS Unit Testing Framework. For testing React components with rendering we add __Testing Library__ to __Jest__.

Jest and Testing Library are deeply and fully documented :
- [Jest Documentation](https://jestjs.io/docs/en/getting-started.html) 
- [Testing Library Documentation](https://testing-library.com/docs/intro)

In the project repository, JS unit tests are currently located in *__tests\__* folders in _src/_ and _src/shared-patient-lib/_, but for convenience it could be located elsewhere. Good practices recommend using such a folder near the tested code :)

The _src/setupTests.js_ file is loaded and runs before each test. It contains some global variables initialization.
 
The _src/utils/testUtils.js_ file contains some utilities functions used by the unit tests to mock and render the application behaviour.

 __Run unit tests:__

```sh
docker-compose exec --user "${_UID}" node yarn test
```
__Run unit tests with coverage:__

```sh
docker-compose exec --user "${_UID}" node yarn test-coverage
```

## Launch Nightwatch E2E tests

Stop the project

In the `docker-compose.override.yml`, replace `localhost` in the lines `- 'API_ENDPOINT=http://localhost'` with the IP address of your current VM (run `ip addr` to find it). If you changed the listening port of the nginx service in the api project do not forget to specify the listening port.

**Note** that if you are using the *docker-compose.test.yml* you can also use `API_ENDPOINT=http://api-nginx:80` as proposed in the default *docker-compose.override.yml* file.

Restart the project

To run the project tests, run: 
```shell script
# In a first console, start all the required containers
docker-compose -f docker-compose.yml -f docker-compose-override.yml -f docker-compose.test.yml up

# In another console, update and feed database...
docker-compose -f docker-compose.test.yml exec -T api-php bash -c 'gosu ${SYMFONY_USER} bin/console doctrine:schema:update --force'
docker-compose -f docker-compose.test.yml exec -T api-php bash -c 'gosu ${SYMFONY_USER} bin/console hautelook:fixtures:load --no-interaction --purge-with-truncate'

# ... and then run the tests
docker-compose exec -T --env TESTS_PATH node sh -c 'set -e ; for FILE in $(find ${TESTS_PATH} -name "*.js") ; do ./node_modules/.bin/nightwatch --env=chrome $FILE --suiteRetries 1 ; done'
```

To run only a part of the tests suites:
```shell script
docker-compose exec --user "${UID}" node node_modules/.bin/nightwatch --env chrome tests/features/XXXX
```

**Note** that the test Docker containers include a VNC application that allows to view the tests execution. 
You simply need to point your browser to `http://localhost:18081`; the VNC password is the default one: `secret`.
With this feature you will be able to watch the tests during their execution. It makes it easier to find an error ;)

**Note** that Chrome is opened in a 1024x768 pixels window and the dev tools are automatically opened !