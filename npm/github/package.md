# Package Private Repo
First run:
[Create New Module](#create-new-module)

Future runs:
[Update Existing Module](#update-existing-module)

## Create New Module

1. Create and populate `package.json`.

    1. Initialize `package.json` settings with the npm wizard.

        ```bash
        npm init;
        ```

    2. Open `package.json`

    3. *(Optional)* Make it private (*i.e., prevent an accidental `npm publish` from making the package public*).

        * If using a scope/organization (e.g., `"name": "@scope/package"`), add this line:

            ```json
            "publishConfig": {
               "scope": "<your-organization>"
            }
            ```

        * If **not** using a scope/organization (e.g., `"name": "package"`), add this line:

            ```json
            "private": true,
            ```
            
    4. Add/Set the [version number](https://docs.npmjs.com/getting-started/semantic-versioning)

        ```json
        "version": "1.0.0",
        ```

2. Publish.

    1. Commit changes.

        ```bash
        git add -A && git commit -m '1.0.0';
        ```

    2. **IMPORTANT** Tag the commit with the version number.

        ```bash
        git tag -a v1.0.0 -m 'v1.0.0';
        ```

    3. Publish to GitHub.

        ```bash
        git push origin master;
        git push origin v1.0.0;
        ```

5. Create a new GitHub deploy key.

    1. Create a new SSH key.

      ```bash
      ssh-keygen -t rsa -b 4096 -C 'unneeded@email.com'
      # Generating public/private rsa key pair.
      # Enter file in which to save the key (~/.ssh/id_rsa): ./git_rsa
      # Enter passphrase (empty for no passphrase): 
      # Enter same passphrase again: 
      # Your identification has been saved in ./git_rsa.
      # Your public key has been saved in ./git_rsa.pub.
      # The key fingerprint is:
      # 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00 unneeded@email.com
      # The key's randomart image is:
      # +--[ RSA 4096]----+
      # |                 |
      # |                 |
      # |                 |
      # |                 |
      # |                 |
      # |                 |
      # |                 |
      # |                 |
      # |                 |
      # +-----------------+
      ```

    2. Add to repo's list of deploy keys:

        1. Copy **public** SSH key to clipboard.

            ```bash
            cat ./git_rsa.pub | pbcopy;
            ```

        2. In browser, go to the settings for the repo of the private package.    
        https://github.com/org/repo/settings/keys

        3. Click `Add deploy key`

        4. Paste key into `Key` field

        5. Add `Title`

        6. Click `Add key`

    3. Save both the private (`./git_rsa`) and public (`./git_rsa.pub`) SSH keys somewhere on the machine. The repo(s) which will use the private module will need these keys in [../install.md](../install.md).

## Update Existing Module

1. Open `package.json`

2. Bump up the version [according to semantic versioning](https://docs.npmjs.com/getting-started/semantic-versioning). Example:  
    ```text
    "version": "1.0.0",
    ```

    to:
    ```text
    "version": "1.0.1",
    ```

3. Publish.

    1. Commit changes.

        ```bash
        git add -A && git commit -m '1.0.1';
        ```

    2. **IMPORTANT** Tag the commit with the version number.

        ```bash
        git tag -a v1.0.1 -m 'v1.0.1';
        ```

    3. Publish to GitHub.

        ```bash
        git push origin master;
        git push origin v1.0.1;
        ```

## Resources
* https://docs.npmjs.com/private-modules/intro
* https://docs.npmjs.com/files/package.json
* https://git-scm.com/book/en/v2/Git-Basics-Tagging
