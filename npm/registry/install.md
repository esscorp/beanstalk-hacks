# Install Private Modules
To allow an Elastic Beanstalk environment to npm install your private npm modules:

1. Get npm authentication token.

    * If you have **not created** one, [Create Auth Token](#create-auth-token).

    * If you have **already created** one, copy it to the clipboard.

2. [Use Auth Token](use-auth-token).

3. [Deploy](deploy).

## Create Auth Token

1. Sign in as one of the members of your organization (https://www.npmjs.com/org/your-organization/members).

    ```bash
    npm login;
    ```

2. Copy your `authToken` for the npm registry to the clipboard:

    * OS X:

        ```bash
        url="$(cat ~/.npmrc | grep '//registry.npmjs.org' | tail -n 1)";
        NPM_TOKEN="${url##*authToken=}";
        echo "${NPM_TOKEN}" | pbcopy;
        ```

    * Linux

        ```bash
        url="$(cat ~/.npmrc | grep '//registry.npmjs.org' | tail -n 1)";
        NPM_TOKEN="${url##*authToken=}";
        echo "${NPM_TOKEN}" | xclip -selection clipboard;
        ```

3. **!!!VITAL!!!** ***NEVER*** logout (`npm logout`).

    * If you do, you will need to redo all of the steps on this page for all of your Elastic Beanstalk environments which need access to your private npm modules.

    * Highly consider creating (and paying for) a dummy/machine npm user dedicated to this process.

    * To login as a different user without logging out the current user, do this hack:

        ```bash
        rm ~/.npmrc;
        npm login;
        ```

## Use Auth Token

1. Add authentication token to the Elastic Beanstalk environment.

    1. Go to https://console.aws.amazon.com/elasticbeanstalk/home#/applications

    2. Select application environment.

    3. Navigate to `Configuration` > `Software Configuration`

    4. Under `Enivronment Properties`, add a new property:

        | Property Name  | Property Value           |
        | -------------- | --------------           |
        | NPM_TOKEN      | \<paste-NPM_TOKEN-here\> |

    5. Click the **+** sign

    6. Click `Apply`

2. Add `.npmrc` to the Elastic Beanstalk environment.

    1. Open the repo's source code.

    2. Create a new `.ebextensions/npm.config` file with this **exact** content:

        ```yaml
        # optionally delete npm config file
        commands:
            delete_npm_config:
                command: "rm /tmp/.npmrc"
                ignoreErrors: true

        # create npm config file
        files:
            "/tmp/.npmrc":
                content: |
                    //registry.npmjs.org/:_authToken=${NPM_TOKEN}
        #
        ```

## Deploy
  
1. **IMPORTANT!** Clone the environment. Deploying the current environment will fail for an unknown reason.

    ```bash
    eb clone <current-environment>
    eb use <new-environment>
    ```

2. Deploy to new/cloned environment.

    ```bash
    eb use <new-environment>
    eb deploy;
    ```

3. If deployment was successful, point the site's url to the new/cloned environment.

    1. Go to https://console.aws.amazon.com/elasticbeanstalk/home#/applications

    2. Select either the old or new/cloned application environment.

    3. Click `Actions` at the top right.

    4. Click `Swap Environment URLs`.

    5. Verify the `Environment name` and `Environment URL` are correct.

    6. Click `Swap`.

## Resources
* https://docs.npmjs.com/orgs/what-are-orgs
* http://blog.npmjs.org/post/118393368555/deploying-with-npm-private-modules
* http://stackoverflow.com/questions/32809616/access-private-npm-modules-on-aws-node-container
* http://stackoverflow.com/questions/24993092/how-to-use-a-private-npm-registry-on-elastic-beanstalk
