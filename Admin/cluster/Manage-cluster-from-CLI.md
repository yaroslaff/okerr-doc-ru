# Manage cluster from CLI

## Information

Brief cluster info
```shell
(okerr) okerr@charlie:~$ ./manage.py impex --info
Host: 'charlie' Cluster: 'TEST' ci: 2
Profiles: 0 / 58

(okerr) okerr@charlie:~$ ./manage.py impex --cluster
{
    "1": {
        "ci": 1,
        "name": "bravo",
        "url": "https://bravo.okerr.com/",
    },
    "2": {
        "ci": 2,
        "name": "charlie",
        "url": "https://charlie.okerr.com/",
    },
    "3": {
        "ci": 3,
        "name": "echo",
        "url": "https://echo.okerr.com/",
    }
}
```
Here we see, our host has name 'charlie in cluster 'TEST' and has cluster index (ci) 2.

View all cluster users and their host server cluster index (ci):

```shell
(okerr) okerr@okerr:~$ ./manage.py impex --list
0 email@test.com
2 email@example.com
```

This command will list only local users:
```shell
./manage.py impex --cilist
email@example.com
```

## Change host server for user
On new server:
~~~
(okerr) okerr@charlie:~$ ./manage.py impex --setci --user user@example.com --remote
Profile Profile for user user@example.com set to ci 2
Project xenon@eml.ru set to ci 2
Done. Took 0.01s
update to rs bravo: https://bravo.okerr.com/
remote: OK
update to rs echo: https://echo.okerr.com/
remote: OK
~~~

Actual user data is not transferred, just ci field is updated on all servers.

## Syncing user records

Sync one user from remote server:
~~~
(okerr) okerr@bravo:~$ ./manage.py impex --import --url https://echo.okerr.com/ --user user@example.com --overwrite
import user@example.com
Age: 0.08, total processed: 29 (377.46/sec). saved: {'Profile': 1, 'Project': 1, 'ProjectTextID': 1, 'Policy': 3, 'PolicySubnet': 6, 'ProfileArg': 11, 'Indicator': 3, 'ProjectMember': 1, 'Membership': 1, 'Oauth2Binding': 1}
~~~

Sync all users from remote server:
~~~
(okerr) okerr@bravo:~$ ./manage.py impex --import --url https://echo.okerr.com/ --user xenon@eml.ru --overwrite
import xenon@eml.ru
Age: 0.08, total processed: 29 (377.46/sec). saved: {'Profile': 1, 'Project': 1, 'ProjectTextID': 1, 'Policy': 3, 'PolicySubnet': 6, 'ProfileArg': 11, 'Indicator': 3, 'ProjectMember': 1, 'Membership': 1, 'Oauth2Binding': 1}
~~~

Sync all users from all servers (according to syncmap). okerr does this automatically periodically, but if you want to force this ASAP:
~~~
(okerr) okerr@bravo:~$ ./manage.py impex --syncmap
syncmap from charlie charlie.okerr.com
syncmap from echo echo.okerr.com
sync user user@example.com from echo.okerr.com rci: 3
~~~

## Backup user
You can use curl/wget from trusted IP (listed in TRUSTED_IPS in config) to get user data:
~~~shell
(okerr) okerr@bravo:~$ curl https://bravo.okerr.com/api/admin/export/okerrdemo@maildrop.cc
{
    "ci": 1,
    "sendalert": false,
    "sendsummary": false,
    "nextsummary": 1517476380,
    "sumtime": 33180,
....
~~~

## Restore user

~~~shell
./manage.py impex --import --file demo/okerrdemo\@maildrop.cc  --overwrite
import okerrdemo@maildrop.cc
Age: 0.35, total processed: 64 (183.65/sec). saved: {'Profile': 1, 'Project': 1, 'ProjectTextID': 2, 'Policy': 2, 'PolicySubnet': 4, 'ProfileArg': 11, 'Indicator': 39, 'ProjectMember': 1, 'Membership': 1, 'StatusPage': 1, 'Oauth2Binding': 1}
~~~