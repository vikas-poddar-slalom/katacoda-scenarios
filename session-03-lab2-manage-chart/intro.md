# Managing charts

Now that you have explored some basic commands, we will explore managing Helm charts and deployments

## Contents of a Helm Chart
Helm charts are packaged directories that, at a minimum, must contain these files:
1. Chart.yaml - A YAML file containing information about the chart
1. values.yaml - The default configuration values for this chart
1. templates/ - A directory of templates that, when combined with values, will generate valid Kubernetes manifest files.
1. charts/ - A directory containing any charts upon which this chart depends

## Packaging and Deployment
Helm charts are packaged simply by zipping up files into a tarball (.tgz) file
