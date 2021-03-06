---
changelog:
last_modified_at:
title: Can I add projects with submodules or with private repo dependencies?
tag:
- git
- faq
- ssh
description: If you have a project with one or more submodules or other private repository
  dependencies (for example, CocoaPods repositories), you have to grant access to
  all repositories for a successful build.
redirect_from: []

---
If you have a project with one or more submodules or other private repository dependencies (for example, CocoaPods repositories), you have to grant access to all repositories for a successful build.

There are two ways to achieve this:

* Register **the same SSH key** for every repository you have to access during the build,
  as "Deployment keys". This is the best, most secure way but not supported by all git hosting services (GitHub, for example, doesn't support it).
* Register an SSH key for a user account. Simply add a "bot" / "machine" user with the SSH key to the repositories. **Add the SSH key you would like to use to the user and add the user to the projects**. It is enough to assign _read_ permissions to the bot user. After that, you can use the SSH key to clone to the repository or any submodule.

{% include message_box.html type="note" title="Use a machine user with read only access" content=" It is not required to use a special bot/machine user: you can add the SSH key to your own account on the git hosting service. The best practice, however, is to use a **machine user, with** **read only** access, for those repositories you want to access during the build. "%}

[GitHub itself recommends this method](https://developer.github.com/v3/guides/managing-deploy-keys/#machine-users) for accessing multiple repositories.

On GitLab and Bitbucket it's possible to register a single SSH key
as a "Deployment key" to multiple repositories, without the need to create a "bot" / "machine" user.

## Using a machine user to access private repositories

[A machine or bot user](https://developer.github.com/v3/guides/managing-deploy-keys/#machine-users) is a GitHub user that is not used by humans, instead it is exclusively used for automation. This is the best way to access a private repository: you create a machine user, add a public SSH key to the user, and then provide the user read access to the repository.

### Adding the machine user to your repository

1. Create a new GitHub user account, one that will serve as the machine user.
2. Go to your repository on GitHub and select the **Settings** tab.
3. On the left side menu, select **Collaborators & teams**.

   ![](/img/Collaborators.png)
4. Scroll down to the **Collaborators** window.
5. In the search input field, search for the username of your newly created account.
6. Click **Add Collaborator**.
7. Change the user permission to **Read**.

   By default, the invited collaborator's permission is **Write**. You can keep it that way, of course, but a Read permission is enough for Bitrise.

### Adding the SSH key to the machine user

In order for Bitrise to be able to use the machine user to access your repository, you must add the same SSH key to the machine user and the app on Bitrise.

When adding a new app:

1. Start the process of [adding your app on Bitrise](/getting-started/adding-a-new-app/). 
2. When prompted to setup repository access, you can choose either **Automatic** or **Add own SSH**:

   ![](/img/repo-access.png)
   * If you choose **Add own SSH**, you can [generate your own SSH keypair](https://devcenter.bitrise.io/faq/how-to-generate-ssh-keypair/). Provide the generated SSH key for the app and [add the public key to your GitHub machine user](https://help.github.com/en/articles/adding-a-new-ssh-key-to-your-github-account).
   * If you choose **Automatic**, click **I need to** when asked if you need to use an additional private repository. Copy the SSH [public key to your GitHub machine user](https://help.github.com/en/articles/adding-a-new-ssh-key-to-your-github-account).
3. Finish the process.

If your app already exists:

1. Open your app on Bitrise.
2. Go to the **Settings** tab. 
3. Scroll down to **SSH settings**.
4. Click the **Click to show SSH public key** button. 
5. Copy the SSH public key and [add it to your GitHub machine user](https://help.github.com/en/articles/adding-a-new-ssh-key-to-your-github-account).

## Git cloning submodules and repository dependencies

There is one important detail to keep in mind when you want to give access to all submodules or private repository dependencies for an app: the app's privacy settings determine what git URL should you use.

* If you have a **private** app: **use SSH URLs everywhere!** Most services support SSH key based authentication **only** for SSH URLs
  (for example, `git@github.com:bitrise-io/bitrise.git`).
  Therefore **every private repository you want to use have to be addressed with the SSH URL**.
  If you have direct private git repo references in your CocoaPods `Podfile` you'll have to
  use the SSH URL there as well! The same applies for submodules and every other private
  git repository URL you want to use with the SSH key you register on [Bitrise.io](https://www.bitrise.io/)!
* If you have a **public** app: **use HTTPS URLs everywhere!** SSH URLs require SSH keys even if the repository is public. For security reasons, public apps CANNOT have SSH keys. As HTTPS git clone URLs do not require any authentication in the case of public repositories, they should be used for public Bitrise apps.

{% include message_box.html type="info" title="Cloning issues" content="If you encounter issues with git cloning - for example, not all submodules are cloned - try the following command after cloning:

`git submodule update --recursive --remote --merge --force`"%}

## Creating SSH keys for a new private app

There are three options to grant [Bitrise](https://www.bitrise.io) access to your repository:

* _Auto-add SSH keypair_: **Don't use this option if you use submodules.**
  This option adds the SSH key to the main repository only.
* _Generate SSH keypair_: this generates a key for you on the [Bitrise](https://www.bitrise.io) website and you will have to copy it manually to the given user.
  **This is the recommended option if you want to use submodules or have to access multiple repositories during your build.**
* _Use your own SSH keypair_: can be used if you also have the private key of the given user.
  You just have to paste the private key and [Bitrise](https://www.bitrise.io) will be able to access the repositories.
  **Keep in mind that the SSH key has to be an RSA key, without a passphrase!**
  You can find an example of how you can generate a key like that [here](/faq/how-to-generate-ssh-keypair/).

## Managing SSH keys of an existing private app

You can find the public SSH key of the app in the `Settings` of the given
app on [Bitrise](https://www.bitrise.io). Scroll down to the `SSH settings` section
and click `Show SSH Public Key`.

Copy the key to the given user and you are ready to build!

If necessary, update the given app's SSH key by clicking the `Change SSH Keypair` button and choosing one of the three options.