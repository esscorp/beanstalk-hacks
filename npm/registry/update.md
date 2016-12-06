# Update Private NPM Module
To publish an update to a private npm module:

1. Open `package.json`

2. Bump up the version [according to semantic versioning](https://docs.npmjs.com/getting-started/semantic-versioning). Example:

    ```text
    "version": "1.0.0",
    ```

    to:

    ```text
    "version": "1.0.1",
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

5. Update repos using this module.

    1. In each repo's `package.json`, update the version number. Example:

        ```json
        "@organization/package": "1.0.0"
        ```

        to:

        ```json
        "@organization/package": "1.0.1"
        ```

    2. Deploy each repo.

        ```bash
        eb deploy;
        ```

## Resources
* https://docs.npmjs.com/private-modules/intro
* https://docs.npmjs.com/files/package.json
