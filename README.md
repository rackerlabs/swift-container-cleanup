First, let's blow away an object's data to simulate our problem.

```bash
redbo@saio:~$ rm -rf /srv/hb/*/*/objects/776/4a2/c208c33f4e5a20e679a907ef5ef904a2
```

We can just run the script in delete mode to clean up any bad objects it finds while auditing.

```bash
redbo@saio:~$ swift-container-cleanup -d AUTH_test
Auditing account "AUTH_test"
Auditing container "0-2653996122652770790"
[...stuff stuff stuff...]
Missing object: /AUTH_test/14-2653996122652770790/4f666652145a13bf
  ...Deleting /AUTH_test/14-2653996122652770790/4f666652145a13bf
[...stuff stuff stuff...]
Completed container: /AUTH_test/8-2653996122652770790

              Accounts Checked: 1
                Failed To List: 0

            Containers checked: 15
                Failed To List: 0

               Objects Checked: 4992
               Missing Objects: 1
       Deleted From Containers: 1
```

OR, we can output all bad objects to a file.

```bash
redbo@saio:~$ swift-container-cleanup -e badobjects.txt AUTH_test
Auditing account "AUTH_test"
Auditing container "0-2653996122652770790"
[...stuff stuff stuff...]
Missing object: /AUTH_test/12-2653996122652770790/3471f468e50cce9a
[...stuff stuff stuff...]
Completed container: /AUTH_test/9-2653996122652770790

              Accounts Checked: 1
                Failed To List: 0

            Containers checked: 15
                Failed To List: 0

               Objects Checked: 4991
               Missing Objects: 1
       Deleted From Containers: 0
```

```bash
redbo@saio:~$ cat badobjects.txt 
/AUTH_test/12-2653996122652770790/3471f468e50cce9a
```

Okay, we have a list of bad objects.  A list of one bad object.

We can double-check it, mull it over, then decide to delete the items.  They will be checked again before being deleted.

```bash
redbo@saio:~$ cat badobjects.txt | swift-container-cleanup -d
Missing object: /AUTH_test/12-2653996122652770790/3471f468e50cce9a
  ...Deleting /AUTH_test/12-2653996122652770790/3471f468e50cce9a

              Accounts Checked: 0
                Failed To List: 0

            Containers checked: 0
                Failed To List: 0

               Objects Checked: 1
               Missing Objects: 1
       Deleted From Containers: 1
```

If we want to audit a bunch of accounts or containers, we can also feed those in by stdin.  They should be URL-encoded.

```bash
redbo@saio:~$ cat account_list 
AUTH_test
AUTH_test2
AUTH_test3
redbo@saio:~$ swift-container-cleanup -e badobjects.txt < account_list 
```
