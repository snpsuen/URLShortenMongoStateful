## Alternative URL shortner prototype
In this version, the Mongo primary replica will initiate the replica and initialise the relevent database in one go when its K8s stateful pod starts up. To this end, the container entry point is set to a customised shell script that goes through the following key steps on the primary replica.
1.  Start an initial mongod daemon with a replica set and key file in the background
2.  Create an admin user and initiate the replica set
3.  Initiase a database collection for the URL shortening workloads
4.  Shut down the background mongo daemon
5.  Restart the mongod daemons with the replica set, key files and other flags like bind_ip_all
Other replicas will proceed straight away to Step 5.

The only other code change is made to the Flask frontend app.py, where the the @app.before_first_request is dropped.

The deployment procedure is exactly the same as my ealier repo, https://github.com/snpsuen/URLShortenStateful.


