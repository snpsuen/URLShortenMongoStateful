## Alternative URL shortner prototype
Based on an alternative golden image, the Mongo primary replica will initiate the replica and initialise the use case database in one go when its K8s stateful pod starts up. To this end, the container entry point is set to a customised shell script that goes through the following key steps on the primary replica.
1.  Fork a child mongod daemon invoked with a replica set and key file
2.  Create an admin user and initiate the replica set
3.  Initialise a database collection to store the URL shortener mappings
4.  Shut down the child mongo daemon
5.  Start the main process of a mongod daemon with the replica set, key files and other flags like bind_ip_all 

Other replicas will proceed straight away to Step 5. More specifically, the initialisation script is defined via a K8s config map that will be mounted as /docker-entrypoint-initdb.d/mongostate-init.sh by the Mongo stateful pods. Please refer to the [config map manifest](./manifest/mongostate-configmap.yaml) for details, 

The only other code change occurs to the Flask frontend [app.py](./app.py), where the the @app.before_first_request hook is dropped.

The deployment procedure is exactly the same as my [earlier repo](https://github.com/snpsuen/URLShortenStateful). Just use the docker image urlshortenstateful:v2 and kubectl apply -f the yaml manifests from [./manifest](./manifest) of THIS repo.
