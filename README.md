# posit_workbench_metrics

Workbench exports RRD files with metrics: <https://docs.posit.co/ide/server-pro/auditing_and_monitoring/monitoring_configuration.html#analyzing-rrd-files> 

Alternatively the data can be sent to an export service, such as graphite: <https://docs.posit.co/ide/server-pro/auditing_and_monitoring/monitoring_configuration.html#using-graphite> 

Explored in this blog post: <https://rviews.rstudio.com/2018/06/20/reading-rrd-files/?_gl=1*1aep1oo*_ga*MTY5MDk0MTUwNi4xNzAzMDIyNzQ5*_ga_2C0WZ1JHG0*MTcwMzI2ODM3MC4xMDQuMS4xNzAzMjcyMDQ4LjAuMC4w> 

Useful python reference for getting useful metrics out of RRD files: <https://adl1995.github.io/plotting-graphical-data-using-rrdtool-and-a-python-collectd-plugin.html>

Chronicle internal reference: <https://positpbc.atlassian.net/wiki/spaces/Platform/pages/594575714/Workweek+2024+Chronicle+Try-It-Out+Session+Notes> 

Using Chronicle data: <https://colorado.posit.co/rsc/chronicle-installation-guide/#using-chronicle-data> 

## How is the current dashboard being generated? 

<https://github.com/rstudio/rstudio-pro/tree/main/src/cpp/server/admin>


## RRD files 

### How to get them from k8s

Log in with your credentials to the appropriate source for your k8s cluster

```
kubectl get pods -n soleng-system
kubectl describe pod prepull-daemonset-t75cx -n soleng-system
kubectl get pods -n rstudio
kubectl describe pod rstudio-workbench-6bf4587f8d-2j26k -n rstudio
```

From there, you can SSH into the running pod by using the command below:
```
kubectl exec -it <podname> -- /bin/bash
kubectl exec -it rstudio-workbench-6bf4587f8d-2j26k -n rstudio -- /bin/bash
```

We could use SCP: 

```
scp -i "~/.ssh/id_ed25519" ~/Downloads/wbi ubuntu@18.116.241.140:~
sudo ./wbi setup
```

Copying a Single File with scp
To send a file called "sample.txt" to a computer called "fedora-34" on the local network, the syntax is:
```
scp ./sample.txt dave@fedora-34.local:/home/dave/Downloads/
```

To copy a file from a remote server, simply put the remote server as the source, and put the local path where you want the file copied as the target. We're going to copy a file called "development-plan.md" from the remote computer to the current directory on the local computer.
```
scp dave@fedora-34.local:/home/dave/Downloads/development-plan.md .
```

Find the hostname: 
```
hostname -I
```

```
scp -i rsessions.count.rrd ~/Downloads/wbi ubuntu@18.116.241.140:~
scp abc.txt xyz@<ip_address_of_xyz>:/home/xyz/Desktop
```

Alternatively, we can copy the files from k8s pod to local system using `kubectl cp`: <https://stackoverflow.com/questions/52407277/how-to-copy-files-from-kubernetes-pods-to-local-system>

Copy /tmp/foo from a remote pod to /tmp/bar locally
```
kubectl cp <some-namespace>/<some-pod>:/tmp/foo /tmp/bar
```

Let's say you are copying file from bin folder to local system. The command is
```
kubectl cp default/POD_NAME:bin/FILE_NAME /Users/username/FILE_NAME
```

You can connect to POD to verify if you are specifying correct file name
```
kubectl exec -ti POD_NAME bash
```

For example for all the RRD files you would run this to copy each one individually: 

```
kubectl cp rstudio/rstudio-workbench-6bf4587f8d-2j26k:/var/lib/rstudio-server/monitor/rrd/rsessions.count.rrd /tmp/rsessions.count.rrd
kubectl cp rstudio/rstudio-workbench-6bf4587f8d-2j26k:/var/lib/rstudio-server/monitor/rrd/system.cpu.usage.rrd /tmp/system.cpu.usage.rrd
kubectl cp rstudio/rstudio-workbench-6bf4587f8d-2j26k:/var/lib/rstudio-server/monitor/rrd/system.load.rrd /tmp/system.load.rrd
kubectl cp rstudio/rstudio-workbench-6bf4587f8d-2j26k:/var/lib/rstudio-server/monitor/rrd/system.memory.rrd /tmp/system.memory.rrd
kubectl cp rstudio/rstudio-workbench-6bf4587f8d-2j26k:/var/lib/rstudio-server/monitor/rrd/system.swap.rrd /tmp/system.swap.rrd
```

```
kubectl cp rstudio/rstudio-workbench-588d7b8ccc-chmnc:/var/lib/rstudio-server/audit/r-sessions/r-sessions.csv /tmp/r-sessions.csv
```

And we can exec in to check the file names with: 
```
kubectl exec -it rstudio-workbench-588d7b8ccc-chmnc -n rstudio -- /bin/bash
```


