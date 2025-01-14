# Upgrading

Upgrading Pegasus is currently a manual process though there are plans to improve it in the future.

New projects are recommended to use the branch-based approach.
Older projects can use the patch-based approach.

The upgrade process can also be used when changing any Pegasus configuration variables.

## Using branches (recommended)

With this option you maintain a "pure" Pegasus branch in your repository with no other modifications.
Then, you merge this branch into your main app when you upgrade.

### 1. Create a branch for the upgrade

First [checkout the first commit](https://stackoverflow.com/questions/43197105/how-do-you-jump-to-the-first-commit-in-git)
in your repository and create a new branch from there.

After finding and checking out the initial commit, run:

```
git branch pegasus
git checkout pegasus
```

*Note: if you created the `pegasus` branch when you set up your codebase you can skip this step.*

Next, make sure the branch is up-to-date with your current Pegasus version:

1. Download your Pegasus project on your *current* version and unzip the code.
2. Copy the `.git` folder from your main project into the downloaded codebase.
3. Make sure you are on the `pegasus` branch (`git checkout pegasus`)
4. Commit all changes (`git add .` then `git commit -am "ready to upgrade"`)

### 2. Upgrade the code in the branch

1. Upgrade your project on saaspegasus.com
2. Download the latest codebase and unzip the code.
3. Copy the `.git` folder from step 1 into this new folder.
4. Commit all changes (`git add .` then `git commit -am "upgrade to latest Pegasus"`)

### 3. Merge into your main branch

1. Checkout the main branch (`git checkout main`)
2. Merge the code (`git merge pegasus`)

In the merging step you should look at the modifications being made, and you may have to manually resolve conflicts that come up.

## Using patches (if you can't use branches)

You can also follow a similar process to the above using Git patches.
Patches do not require working in the same repository or having a previously created branch.

At a high level you will:

1. Create a patch file containing the changes in the upgrade.
2. Apply the patch to your app.

Here we'll walk through the steps in more detail.

### 1. Creating the patch file

Follow these steps to create your patch file:

1. Download a "clean" version of your Pegasus project on your *current* version, and commit it to a git branch or repository.
2. Upgrade your Pegasus version (or change your configuration), and download the new codebase.
3. Copy your `.git` directory from your "clean" project in step 1 into your new project in step 2.
   E.g. `cp -r path/to/yourapp/.git path/to/newapp/`.
4. In your new project directory, *commit all of the changes* in a single commit. 
5. Create a patchfile for the commit using [git-format-patch](https://git-scm.com/docs/git-format-patch).
   The recommended command to run is `git format-patch -1 HEAD`.

You should now see a file in your repository root with a name like `0001-branch-details.patch`. This is your patch file.

### 2. Applying the patch file

Now return to your main branch in your application's repository.

First, use [git-apply](https://git-scm.com/docs/git-apply) to apply the patch.
The recommended command to run is:

```
git apply --ignore-space-change --ignore-whitespace --reject /path/to/<patchname>.patch
```

substituting the path/name of the patchfile created above.

This command will do a best-effort application of the patch. 

For each affected file:

1. If updates could be applied cleanly, the file will be updated with the contents of the applied patch.
2. If updates could not be applied cleanly, a new diff file called `<filename>.rej` will be created, showing the diff that could not be applied. 

If the file was partially updated then the file will be modified *and* the remaining changes will
be visible in the `<filename>.rej` file.

The last step of the upgrade process is to go through each file and:

1. If the file has been modified, look at the modifications, see if you want them, and commit/reject them as necessary.
2. If the file has a `<filename>.rej` file, look at the proposed diff and see if you want to manually apply it, or ignore it.

After you have merged all changes to a file, you should delete the `<filename>.rej` file.

To understand the format of the `<filename>.rej` files, take a look at the [unified diff format](https://en.wikipedia.org/wiki/Diff#Unified_format).
Basically changes will look like the below, with a line starting with a minus sign, indicating a removal, and a plus
sign indicating an addition.

In this example, the type annotations were added to the function signature:

```
-def is_member(user, team):
+def is_member(user: CustomUser, team: apps.teams.models.Team) -> bool:
```

## Other notes

After upgrading you may also need to reinstall requirements (`pip install -r requirements.txt`),
npm packages (`npm install`), etc. depending on what has changed.

You will also need to rebuild your front end if you've made any changes there (`npm run dev` or `npm run build`)
