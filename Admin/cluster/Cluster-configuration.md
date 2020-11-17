# Okerr cluster configuration

## Clusters
Cluster machines shares full user profiles (projects and indicators are belong to profile too). Changes from one machine are replicated to other machines. Each user profile 'belongs' to one machine in cluster (called 'host server' for this profile), and if user logged in to other machine, will be redirected to his host server.

Clusters are serving two purposes:
- **Scalability**: While each one server can handle requests for limited number of users, cluster of many servers can overcome this limitation.
- **Quick recovery**: Unfortunately, any server can crash, reboot, has power or network problems or whatever. Since all machines in cluster has all profiles, it's very quick to recover user account on other server - all data is in place, and need just to change 'host server' setting for account.

## Config files
Cluster machines is defined in settings as `MACHINES` variable. 

When running small okerr server, cluster consist of just one server:
```
HOSTNAME = 'localhost'
CLUSTER_NAME = 'LOCAL'
MACHINES = {
    0: {
        'ci': 0,
        'name': HOSTNAME,
        'url': 'http://localhost.okerr.com/',
    }
}
```

Each machine in cluster has name (hostname), url and cluster index (ci).

## Profiles synchronization

```
SYNC_MAP = {
    'alpha': ['bravo','charlie'],
    'bravo': ['charlie',],
    'charlie': ['bravo',],
    'echo': ['bravo', 'charlie']
}
```

Each server (e.g. 'alpha') replicates profiles from listed servers ('bravo' and 'charlie'). Only profiles which belongs to that host will be synced. (If user belongs to host 'bravo', 'alpha' will update it from 'bravo', but will not update it from 'charlie').
