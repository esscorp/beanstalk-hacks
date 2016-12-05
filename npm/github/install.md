# Install from Private Repo
To allow an Elastic Beanstalk environment to npm install from a private GitHub repo:

1. Add GitHub deploy key:

    1. Create a new `.ebextensions/github.config` file. Copy and paste this exact content into it:

        ```yaml
        files:
            "/root/.ssh/git_rsa":
                mode: "000600"
                content: |
                    <private-key>
            "/root/.ssh/git_rsa.pub":
                mode: "000600"
                content: |
                    <public-key>
            "/root/.ssh/config":
                owner: root
                group: root
                mode: "000600"
                content: |
                    Host github.com
                    User git
                    Hostname github.com
                    IdentityFile /root/.ssh/git_rsa
            "/root/.ssh/known_hosts":
                owner: root
                group: root
                mode: "000644"
                content: |
                    <github-credentials>
        commands:
            10-register-key:
                command: "eval $(ssh-agent -s); ssh-add /root/.ssh/git_rsa;"
            # 99-verify-authenticated:
            #     command: "dst=/tmp/<repo>-tmp; [ -d $dst ] && rm -r $dst; git clone 'git@github.com:<org>/<repo>.git' $dst;"
        ```

    2. Add the `<private-key>`.

        1. Find the **private** SSH key (the one *without* the `.pub` extension) created in [./package.md](./package.md).

        2. Copy this key to the clipboard.

            * OS X

                ```bash
                cat ./git_rsa | pbcopy;
                ```

            * Linux

                ```bash
                cat ./git_rsa | xclip -selection clipboard;
                ```

        3. Paste the key into the new `.ebextensions/github.config` file.
            * Replace `<private-key>`
            * Make sure each line is indented correctly (in yaml format)
            * The pasted content will look like this:

                ```text
                -----BEGIN RSA PRIVATE KEY-----
                <49-lines>
                -----END RSA PRIVATE KEY-----
                ```

    3. Add the `<public-key>`.

        1. Find the **public** SSH key (the one *with* the `.pub` extension) created in [./package.md](./package.md).

        2. Copy this key to the clipboard.

            * OS X

                ```bash
                cat ./git_rsa.pub | pbcopy;
                ```

            * Linux

                ```bash
                cat ./git_rsa.pub | xclip -selection clipboard;
                ```

        2. Paste the key into the new `.ebextensions/github.config` file.

            * Replace `<public-key>`

            * Make sure each line is indented correctly (in yaml format)

            * The pasted content will look like this:

                ```text
                ssh-rsa areallylonghashaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa unneeded@email.com
                ```

    4. Add `<github-credentials>`.

        1. Copy GitHub's public SSH credentials to the clipboard.

            * OS X

                ```bash
                ssh-keyscan -H github.com | pbcopy;
                ```

            * Linux

                ```bash
                ssh-keyscan -H github.com | xclip -selection clipboard;
                ```
        2. Paste the credentials into the new `.ebextensions/github.config` file.
            * Replace `<github-credentials>`
            * Make sure each line is indented correctly (in yaml format)
            * The pasted content will look like this:

                ```text
                |1|alonghashaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa ssh-rsa areallylonghashbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb
                ```

    5. Fix `99-verify-authenticated` command.

        1. Replace `<org>` with your GitHub organization/user.

        2. Replace `<repo>` with the name of the private repo.

        3. *(Optional)* Uncomment the command in order to verify that the deploy key is working.

2. Clone the current environment. *(The tiniest mistake will cause deployment to fail. Do not risk crashing the production server; clone a new one instead.)*

    ```bash
    eb clone <current-environment>
    eb use <new-environment>
    ```

3. Add private module to `package.json`. For the version, use this format:

    ```text
    git+ssh://git@github.com:<org>/<repo>.git#<tag>
    ```

    Example:

    ```json
    "@esscorp/gphone": "git+ssh://git@github.com:esscorp/gphone.git#v0.0.9",
    ```

    *[./package.md](./package.md) shows how to tag the version number*

4. Commit changes.

    ```bash
    git add -A && git commit;
    ```

5. Deploy

    ```bash
    eb deploy;
    ```

6. If deployment was successful, point the site's url to the new environment.

7. Cleanup. It is now safe to remove the SSH keys created in [./package.md](./package.md).

    ```bash
    rm ./git_rsa;
    rm ./git_rsa.pub;
    ```

## Resources
* https://docs.npmjs.com/orgs/what-are-orgs
* http://blog.npmjs.org/post/118393368555/deploying-with-npm-private-modules
* http://stackoverflow.com/questions/10386310/how-to-install-a-private-npm-module-without-my-own-registry/10391718#10391718
* http://stackoverflow.com/questions/13476138/setting-up-private-github-access-with-aws-elastic-beanstalk-and-ruby-container/13507647#13507647
* http://stackoverflow.com/questions/32809616/access-private-npm-modules-on-aws-node-container
* http://stackoverflow.com/questions/24993092/how-to-use-a-private-npm-registry-on-elastic-beanstalk

## Additional Resources
* http://fiznool.com/blog/2015/05/20/an-alternative-to-npm-private-modules/
* https://gist.github.com/fiznool/88442338db96a898f1dc
* https://gist.github.com/rohang07/fdf1d1caf0048968424d#file-01_setupssh-config
* http://stackoverflow.com/questions/791959/download-a-specific-tag-with-git
