# Dockerfile use cases

## Dockerfile-standalone
`./Dockerfile-standalone` is for building standalone image that will be used and run in a dedicated POD as MongoDB Plugin Service.

This POD is (listening to 3333/TCP) responsible to process the queries that came from Grafana. Grafana should have access/connectivity to this pod's service.

Note: When running the plugin service separately, consider that the plugin code itself is also available in the Grafana POD's plugins directory: `/var/lib/grafana/plugins/`.

- This is essential for grafana to detect the MongoDB Plugin, so on related `Datasources` can be configured as query datasource pointing to MongoDB Plugin POD.

Standalone image and pod is necessary to be deployed in customer clusters so each of them will be able to connect to the corresponding customer's MongoDB Atlas.

### How to build
Check the latest available tag in the repository (Artifactory) and increment the version and run the following command by substituding the `<INCREMENTED_TAG_HERE>`. Note that this image name has **`-standalone`**  extension at the end of its name which is deffering it from the other one used in the regular image that is intended to run in the Grafana pod.

Example: If latest tag is `0.1` INCREMENTED_TAG will be `0.2`.
```sh
docker build -t docker-internal.artifactory.igentify.net/fresh-grafana-mongodb-datasource-standalone:<INCREMENTED_TAG_HERE> .
```

_Note: Helm chart repository **link** that is using this image to deploy Mongo Plugin as a standalone service will be placed here after successfully tested and published!_

## Dockerfile
`./Dockerfile` is for building image that is intended to be used and run in the Grafana server's POD alongside the Grafana container to be directly accessible.

This is best way to configure different MongoDB connections as seperated Datasources when we have accessibility to Databases from where the Grafana is deployed.

### How to build
Check the latest available tag in the repository (Artifactory) and increment the version and run the following command by substituding the `<INCREMENTED_TAG_HERE>`.

Example: If latest tag is `0.1` INCREMENTED_TAG will be `0.2`.
```sh
docker build -t docker-internal.artifactory.igentify.net/fresh-grafana-mongodb-datasource:<INCREMENTED_TAG_HERE> .
```

#### Reminder:
To deploy the updated version of this image consider to update the `.Values.mongodbPlugin.image.tag` parameter in the parrent folder's `values.yaml` file.

---

Mongodb Plugin repository that has been used is [here](https://github.com/JamesOsgood/mongodb-grafana)
