# Create Private NPM Module
To publish a new, private npm module:

1. Sign in as one of the members of your npm organization (https://www.npmjs.com/org/your-organization/members).

    ```bash
    npm login;
    ```

2. Create and populate `package.json`.

    1. Initialize `package.json` settings with the npm wizard. **IMPORTANT** *name* must be **@\<your-organization\>/\<package-name\>**

        ```bash
        npm init --scope=<your-organization>;
        ```

    2. Open `package.json`

    3. Add this setting:

    ```json
    "publishConfig": {
      "scope": "<your-organization>"
    }
    ```

    5. Double check that the name is prefixed with **@\<your-organization\>/**. Example:

    ```json
      "name": "@<your-organization>/<package-name>",
    ```

    6. Triple check that the name is prefixed with **@\<your-organization\>/**. Example:

    ```json
      "name": "@<your-organization>/<package-name>",
    ```


3. Save changes.

    ```bash
    git add -A && git commit;
    git push origin master;
    ```

4. Publish to npm.

    ```bash
    npm publish --access=private;
    ```

## Resources
* https://docs.npmjs.com/private-modules/intro
* https://docs.npmjs.com/files/package.json
