---
title: Deactivate an environment
description: See how to deactivate environments you aren't using.
---

If you have environments you aren't using, you may want to deactivate them to save resources for what you need.
To deactivate an environment, you need to be an admin for the project or the given environment.

{{< note >}}

Your default environment is protected.
It can't be deactivated through the Console or the CLI.
To change which environment is the default, see how to [rename the default branch](./default-environment.md).

{{< /note >}}

Deactivating the project results in the following:

* The environment becomes [inactive](../other/glossary.md#inactive-environment).
  Unless it's reactivated, it's no longer deployed and isn't accessible from the web or via SSH.
* All services running on this environment are deleted.
* All data specific to the environment is deleted.
  If the environment is reactivated, it syncs data from its parent environment.

## Deactivate an environment

To deactivate an environment, follow these steps:

{{< codetabs >}}

+++
title=In the Console
file=none
highlight=false
+++

- Select the project with an environment you want to deactivate.
- From the **Environment** menu, select the environment.
- Click {{< icon settings >}} **Settings**.
- In the row with **Status**, click **Edit {{< icon chevron >}}**.
- Select the checkbox to show you understand the consequences.
- Click **Deactivate & Delete Data**.
- Confirm your choice.

<--->
+++
title=Using the CLI
file=none
highlight=false
+++

Run the following command:

```bash
platform environment:deactivate <ENVIRONMENT_NAME>
```

{{< /codetabs >}}

## Delete the branch

Inactive environments still have branches in Git.
To delete the branch entirely, run the following command:

```bash
git push origin --delete <BRANCH_NAME>
```

## Reactivate an environment

Reactivating an environment [syncs](../other/glossary.md#sync) data from its parent environment.

To reactivate an inactive environment, follow these steps:

{{< codetabs >}}

+++
title=In the Console
file=none
highlight=false
+++

- Select the project with an environment you want to reactivate.
- From the **Environment** menu, select the environment.
- Click {{< icon settings >}} **Settings**.
- In the row with **Status**, click **Edit {{< icon chevron >}}**.
- Click **Activate**.

<--->
+++
title=Using the CLI
file=none
highlight=false
+++

Run the following command:

```bash
platform environment:activate <ENVIRONMENT_NAME>
```

{{< /codetabs >}}
