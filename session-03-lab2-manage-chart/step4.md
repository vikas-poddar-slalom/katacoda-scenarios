# Rollback mychart

In this section, we will see how to rollback a Helm chart to a previous version

1. First lets see the list of available versions for release name **mychart**

  `helm history mychart`{{execute}}

  Expect to see 2 revisions
  ```yaml
  REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION
  1               Mon Jun 22 16:43:20 2020        superseded      mychart-0.1.0   1.16.0          Install complete
  2               Mon Jun 22 16:46:54 2020        deployed        mychart-0.2.0   1.16.0          Upgrade complete
  ```

1. Lets downgrade the chart to version **0.1.0**; Version **0.1.0** was applied as **REVISION 1** so we will rollback to that

  `helm rollback mychart 1`{{execute}}

  Expect to see
  ```bash
  Rollback was a success! Happy Helming!
  ```

1. Verify your chart was downgraded to version **0.1.0**

  `helm list`{{execute}}

  Rollback creates a new **REVISION** and sets the version back to **mychart-0.1.0**
  ```yaml
  NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
  mychart default         3               2020-06-22 16:50:20.775935769 +0000 UTC deployed        mychart-0.1.0   1.16.0
  ```

  Use **helm history** again to see how mychart has changed over time
  `helm history mychart`{{execute}}

  ```yaml
  REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION
  1               Mon Jun 22 16:43:20 2020        superseded      mychart-0.1.0   1.16.0          Install complete
  2               Mon Jun 22 16:46:54 2020        superseded      mychart-0.2.0   1.16.0          Upgrade complete
  3               Mon Jun 22 16:50:20 2020        deployed        mychart-0.1.0   1.16.0          Rollback to 1
  ```

# Congrats!

Congrats, you have now rolled back your chart to a previous version!
