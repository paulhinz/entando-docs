
# Tutorial: How to create a digital-exchange bundle

## Purpose
Generate a simple digital-exchange bundle shareble in an Entando6 environment

## Requirements

You can create the bundle using you favorite text/code editor.
To share the bundle you will need:
1. Node/NPM
2. An NPM registry where to upload the bundle
3. A K8S cluster where to upload the bundle (e.g. minikube, microk8s,minishift) configured correctly for Entando 6
4. A namespace in the cluster reachable from the operator and entando-k8s-service
5. The `entando-bundle-cli` command-line tool to generate the necessary metadata to share the bundle in a K8S cluster

## Steps

### 1. Create bundle folder
To start, let's create a new folder to host your bundle. 
```
mkdir example-bundle && cd example-bundle
```

### 2. Add a descriptor.yaml file

For a bundle to be readable by the entando-component-manager it will need at least a `descriptor.yaml` file in the folder. Let's create it with some minimail information

```
vim descriptor.yaml
```

Here some content for your base descritor.

```
code: example-bundle
description: This is an example of a Entando6 bundle

components:
```

### 3. Add a simple component to the bundle

This bundle will contains only a simple widget.

Let's first create the widget metadata in a dedicated folder
```
mkdir widgets

vim widgets/example-widget.yaml
```

Now let's populate the `example-widget.yaml` metadata with some content:

```
code: example-widget
titles:
  en: Example Widget
  it: Widget d'esempio
group: free
customUi: <h2>Hi from Example Widget</h2>
```
Finally, add a reference to this widget in the bundle `descriptor.yaml` file

```
code: example-bundle
description: This is an example of a Entando6 bundle

components:
    widgets:
        - widgets/example-widget.yaml
```

### 4. Make the bundle an NPM module to be hostable on an NPM registry

From the bundle root, let's initialize a `package.json` file 

```
npm init
```

Follow the instructions on screen, here an example of a possible `package.json` file
```
{
  "name": "@entando/example-bundle",
  "version": "1.0.0",
  "description": "An example of an Entando6 bundle",
  "license": "LGPL-2.1",
  "main": "descriptor.yaml",
  "keywords": [
    "entando6",
    "digital-exchange",
    "entando-de-bundle"
  ]
}
```

### 5. Publish the bundle on an NPM registry

Now your bundle is ready to be published on an NPM registry.

From the root of the bundle (where the package.json and descriptor.yaml files are) you can issue an `npm publish` command.

**Note**: It would be ideal to have a private npm registry where to upload this. Check the [resources section](#resources) for more details;

```
npm publish --registry=<your-registry>
```

### 6. Create the EntandoDeBundle custom resource for K8S

Assuming the "entando-bundle-cli" commandline utility is already installed and available globally on your system, you can now convert the module into an EntandoDeBundle K8S custom resource.
We assume here you have a namespace in a K8S cluster which is readable from the Entando Operator and you have the permissions to create resources there. Let's call this namespace `accessible-ns`

You can also provide a thumbnail for your bundle. Let's use an image available in the [entando-sample-bundle](https://github.com/entando-k8s/entando-sample-bundle) repository.

```
entando-bundle generate @entando/example-bundle --name=example-bundle --namespace=accessible-ns --thumbnail-url=https://raw.githubusercontent.com/entando-k8s/entando-sample-bundle/master/example/survey-bundle/example-bundle.jpg --dry-run > example-bundle.yaml
```

### 7. Upload the bundle to K8S

Now you simply need to upload the bundle into K8S
```
kubectl create -f example-bundle.yaml
```

## Conclusion

You now should have the bundle available in your cluster and accessible from AppBuilder.

## <a name="resources"></a>Resources
- [Setup a local npm registry for testing purposes](../../how-to-create-local-npm-registry.adoc)
- [Entando Bundle CLI project](https://github.com/entando-k8s/entando-bundle-cli)