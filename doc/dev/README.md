# Project documentation

The project directory structure is documented [in this file](structure.md).

## Gitlab and Docker
For the Gitlab remote access it is often mandatory to activate the [2FA (2 factor authentication)](https://docs.gitlab.com/ee/user/profile/account/two_factor_authentication.html). For the 2FA, use the *FreeOTP* application on your smartphone or tablet.
 
Once activated, you will need an access token. Got to your personal profile settings page on Gitlab and select the Access Tokens menu. Create an access token with all the rights (except registry) and use it as a password to authenticate with the command line git when it requires a username / password.

Create an access token with the **registry** right and use it as a password to authenticate with the Docker when it requires a username / password. Log-in to the Docker registry

```shell script
# Replace g.lagaffe with your Gitlab username
# ACCESS_TOKEN is the access token provided by Gitlab
docker login docker.registry.tmm-software.net -u g.lagaffe -p ACCESS_TOKEN
```


## Installation and development

Because all the development tools are based on Docker containers, it is important to share the user identity between the host computer and the Docker containers. The main reason is to manage correctly the shared volumes files permissions. The user identity is locally stored in an environment variable that must be exposed in the shell environment.

```sh

# Push _UID variable in your environement
echo "export _UID=$(id -u)" >>  ~/.bashrc
```
**Note** : you need to log-out / log-in for this variable to get correctly installed in your shell -)

**Note** if some files shared between the host and Docker containers are root:root owned, it may be because this user identifier is not correctly set!

### Install development configuration

```sh
cp docker-compose.override.yml.dist docker-compose.override.yml
```

The _docker-compose.override.yml_ file overrides and completes the default configuration contained in _docker-compose.yml_. It is **always a bad idea** to modify the *docker-compose.yml* file.

# Build the project 

If some new parameters are used or updated such as BRANDING, WS_ENTRYPOINT etc...

```sh
docker container prune
docker images prune -a
docker-compose -f docker-compose.yml -f docker-compose.override.yml -f docker-compose.test.yml build
```

# Start the project

This command will run all the containers required to run the `app-web` application, its test environment and backend : 
 
```sh
docker-compose -f docker-compose.yml -f docker-compose.override.yml -f docker-compose.test.yml up
```
And navigate to "http://localhost:3001"

You can start the application with this simple command if you do not need to run an application backend locally: 
 
```sh
docker-compose up
```

You can specify a white label branding for the running application:

```sh
BRANDING=mentalapps docker-compose up
```

# Stop the project

```sh
docker-compose down
```

# Development helpers

Run the code formatter

```sh
docker-compose exec --user "${UID}" node yarn format
```

Run the linter check

```sh
docker-compose exec --user "${UID}" node yarn lint
```

Run the linter with fix option

```sh
docker-compose exec --user "${UID}" node yarn lint-fix
```

