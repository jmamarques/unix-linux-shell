[TOC]

# Snapshots #
Before creating a snapshot of an instance, we must first stop its execution by selecting the desired instance and pressing **Stop**:

![stop_instance.png](https://bitbucket.org/repo/X577Lde/images/1916613448-stop_instance.png)

## Creating a snapshot ##
To create a snapshot using the GUI press the menu button on the desired instance under the **Actions** column and select **Snapshot**:

![snapshot.png](https://bitbucket.org/repo/X577Lde/images/2668376315-snapshot.png)

Input the desired name for the snapshot (a **Stateful** snapshot saves the execution state of all processes) and press **Snapshot**:

![create_snapshot.png](https://bitbucket.org/repo/X577Lde/images/282403972-create_snapshot.png)

To view the created snapshots select the instance and navigate to the **Snapshots** tab:

![snapshots.png](https://bitbucket.org/repo/X577Lde/images/4230422426-snapshots.png)

## Restoring a snapshot ##
To restore the instance's state to a previous snapshot select the desired snapshot and press **Restore**:

![restore.png](https://bitbucket.org/repo/X577Lde/images/2894546626-restore.png)

# Exports #
Similarly to Snapshots, to export an instance we also must have it in a stopped execution state.


## Exporting an instance ##
Select the **Export** option under the **Actions** menu:

![export.png](https://bitbucket.org/repo/X577Lde/images/1853134750-export.png)

Input the desired name and press **Export Container**:

![export-menu.png](https://bitbucket.org/repo/X577Lde/images/3134644743-export-menu.png)

After the export function finishes executing we can see our instances image at the **Images** side menu:

![exports.png](https://bitbucket.org/repo/X577Lde/images/2398053306-exports.png)

Generating the image as an archive must be done on the host as LXDUI does not have file access to the host. Image archive generation is documented [here](https://bitbucket.org/asolidodev/ironman/wiki/Snapshots%20and%20backups%20via%20CLI#markdown-header-exports).

## Importing a backup ##
Currently importing backup images is not supported by the GUI, it can be done through the CLI as seen [here](https://bitbucket.org/asolidodev/ironman/wiki/Snapshots%20and%20backups%20via%20CLI#markdown-header-importing-a-backup).