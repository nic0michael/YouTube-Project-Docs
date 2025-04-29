---
title: "Install Elasticsearch Logstash and Kibana on K3S"
date: 2023-01-08T18:43:10Z
draft: false
---

## Deploying Elasticsearch

```
kubectl create deployment es-manual --image elasticsearch:7.8.0
```

```
kubectl get pods
```
expect to get:
```
NAME                        READY   STATUS    RESTARTS   AGE
es-manual-89d68bb75-5k5l5   0/1     ContainerCreating   0          

# and again
es-manual-89d68bb75-5k5l5   1/1     Running   1 (26s ago)   3m1s
```

```
kubectl get pods
```


```
kubectl describe deployment es-manual
```

```
kubectl describe pod es-manual-89d68bb75-5k5l5
```
expect to see:
```
Name:             es-manual-89d68bb75-5k5l5
Namespace:        default
Priority:         0
Service Account:  default
Node:             tiger.loseyourip.com/10.154.2.119
Start Time:       Mon, 09 Jan 2023 11:28:55 +0000
Labels:           app=es-manual
                  pod-template-hash=89d68bb75
Annotations:      <none>
Status:           Running
IP:               10.42.2.12
IPs:
  IP:           10.42.2.12
Controlled By:  ReplicaSet/es-manual-89d68bb75
Containers:
  elasticsearch:
    Container ID:   containerd://d583b72f35e18c466ebf292b3f00023faa1851d5cbddf99620e6634f25f24d8e
    Image:          elasticsearch:7.8.0
    Image ID:       docker.io/library/elasticsearch@sha256:945f80960f2ad1bd4b88bd07a9ba160d22d4285bbc8a720d052379006d5d57a6
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       Error
      Exit Code:    78
      Started:      Mon, 09 Jan 2023 11:34:02 +0000
      Finished:     Mon, 09 Jan 2023 11:34:21 +0000
    Ready:          False
    Restart Count:  4
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-ch292 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  kube-api-access-ch292:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                  From               Message
  ----     ------     ----                 ----               -------
  Normal   Scheduled  5m52s                default-scheduler  Successfully assigned default/es-manual-89d68bb75-5k5l5 to tiger.loseyourip.com
  Normal   Pulling    5m52s                kubelet            Pulling image "elasticsearch:7.8.0"
  Normal   Pulled     3m38s                kubelet            Successfully pulled image "elasticsearch:7.8.0" in 2m13.409566513s
  Normal   Pulled     47s (x4 over 3m12s)  kubelet            Container image "elasticsearch:7.8.0" already present on machine
  Normal   Created    46s (x5 over 3m38s)  kubelet            Created container elasticsearch
  Normal   Started    46s (x5 over 3m38s)  kubelet            Started container elasticsearch
  Warning  BackOff    1s (x9 over 2m48s)   kubelet            Back-off restarting failed container
```

```
kubectl logs -f deployments/es-manual
```
expect:
```
{"type": "server", "timestamp": "2023-01-09T11:35:58,054Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "version[7.8.0], pid[7], build[default/docker/757314695644ea9a1dc2fecd26d1a43856725e65/2020-06-14T19:35:50.234439Z], OS[Linux/5.4.0-135-generic/amd64], JVM[AdoptOpenJDK/OpenJDK 64-Bit Server VM/14.0.1/14.0.1+7]" }
{"type": "server", "timestamp": "2023-01-09T11:35:58,059Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "JVM home [/usr/share/elasticsearch/jdk]" }
{"type": "server", "timestamp": "2023-01-09T11:35:58,059Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "JVM arguments [-Xshare:auto, -Des.networkaddress.cache.ttl=60, -Des.networkaddress.cache.negative.ttl=10, -XX:+AlwaysPreTouch, -Xss1m, -Djava.awt.headless=true, -Dfile.encoding=UTF-8, -Djna.nosys=true, -XX:-OmitStackTraceInFastThrow, -XX:+ShowCodeDetailsInExceptionMessages, -Dio.netty.noUnsafe=true, -Dio.netty.noKeySetOptimization=true, -Dio.netty.recycler.maxCapacityPerThread=0, -Dio.netty.allocator.numDirectArenas=0, -Dlog4j.shutdownHookEnabled=false, -Dlog4j2.disable.jmx=true, -Djava.locale.providers=SPI,COMPAT, -Xms1g, -Xmx1g, -XX:+UseG1GC, -XX:G1ReservePercent=25, -XX:InitiatingHeapOccupancyPercent=30, -Djava.io.tmpdir=/tmp/elasticsearch-1011221515620178741, -XX:+HeapDumpOnOutOfMemoryError, -XX:HeapDumpPath=data, -XX:ErrorFile=logs/hs_err_pid%p.log, -Xlog:gc*,gc+age=trace,safepoint:file=logs/gc.log:utctime,pid,tags:filecount=32,filesize=64m, -Des.cgroups.hierarchy.override=/, -XX:MaxDirectMemorySize=536870912, -Des.path.home=/usr/share/elasticsearch, -Des.path.conf=/usr/share/elasticsearch/config, -Des.distribution.flavor=default, -Des.distribution.type=docker, -Des.bundled_jdk=true]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,148Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [aggs-matrix-stats]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,148Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [analysis-common]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,149Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [constant-keyword]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,149Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [flattened]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,150Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [frozen-indices]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,150Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [ingest-common]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,150Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [ingest-geoip]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,151Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [ingest-user-agent]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,151Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [kibana]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,151Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [lang-expression]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,152Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [lang-mustache]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,152Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [lang-painless]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,153Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [mapper-extras]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,153Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [parent-join]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,153Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [percolator]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,154Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [rank-eval]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,154Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [reindex]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,155Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [repository-url]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,155Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [search-business-rules]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,155Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [searchable-snapshots]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,156Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [spatial]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,156Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [tasks]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,156Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [transform]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,156Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [transport-netty4]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,157Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [vectors]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,157Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-analytics]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,158Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-async-search]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,158Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-autoscaling]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,158Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-ccr]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,159Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-core]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,159Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-deprecation]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,159Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-enrich]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,160Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-eql]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,160Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-graph]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,160Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-identity-provider]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,160Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-ilm]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,161Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-logstash]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,161Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-ml]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,161Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-monitoring]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,162Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-ql]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,162Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-rollup]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,162Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-security]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,163Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-sql]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,163Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-voting-only-node]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,163Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "loaded module [x-pack-watcher]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,164Z", "level": "INFO", "component": "o.e.p.PluginsService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "no plugins loaded" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,236Z", "level": "INFO", "component": "o.e.e.NodeEnvironment", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "using [1] data paths, mounts [[/ (overlay)]], net usable_space [465.8gb], net total_space [585.2gb], types [overlay]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,236Z", "level": "INFO", "component": "o.e.e.NodeEnvironment", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "heap size [1gb], compressed ordinary object pointers [true]" }
{"type": "server", "timestamp": "2023-01-09T11:36:01,290Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "node name [es-manual-89d68bb75-5k5l5], node ID [M32MSolNSvGS_h0LFz9ahA], cluster name [docker-cluster]" }
{"type": "server", "timestamp": "2023-01-09T11:36:08,088Z", "level": "INFO", "component": "o.e.x.s.a.s.FileRolesStore", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "parsed [0] roles from file [/usr/share/elasticsearch/config/roles.yml]" }
{"type": "server", "timestamp": "2023-01-09T11:36:08,891Z", "level": "INFO", "component": "o.e.x.m.p.l.CppLogMessageHandler", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "[controller/168] [Main.cc@110] controller (64 bit): Version 7.8.0 (Build 58ff6912e20047) Copyright (c) 2020 Elasticsearch BV" }
{"type": "server", "timestamp": "2023-01-09T11:36:09,796Z", "level": "DEBUG", "component": "o.e.a.ActionModule", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "Using REST wrapper from plugin org.elasticsearch.xpack.security.Security" }
{"type": "server", "timestamp": "2023-01-09T11:36:09,972Z", "level": "INFO", "component": "o.e.d.DiscoveryModule", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "using discovery type [zen] and seed hosts providers [settings]" }
{"type": "server", "timestamp": "2023-01-09T11:36:11,452Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "initialized" }
{"type": "server", "timestamp": "2023-01-09T11:36:11,452Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "starting ..." }
{"type": "server", "timestamp": "2023-01-09T11:36:11,618Z", "level": "INFO", "component": "o.e.t.TransportService", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "publish_address {10.42.2.12:9300}, bound_addresses {[::]:9300}" }
{"type": "server", "timestamp": "2023-01-09T11:36:12,160Z", "level": "INFO", "component": "o.e.b.BootstrapChecks", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "bound or publishing to a non-loopback address, enforcing bootstrap checks" }
ERROR: [2] bootstrap checks failed
[1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
[2]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
ERROR: Elasticsearch did not exit normally - check the logs at /usr/share/elasticsearch/logs/docker-cluster.log
{"type": "server", "timestamp": "2023-01-09T11:36:12,174Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "stopping ..." }
{"type": "server", "timestamp": "2023-01-09T11:36:12,186Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "stopped" }
{"type": "server", "timestamp": "2023-01-09T11:36:12,187Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "closing ..." }
{"type": "server", "timestamp": "2023-01-09T11:36:12,208Z", "level": "INFO", "component": "o.e.n.Node", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "closed" }
{"type": "server", "timestamp": "2023-01-09T11:36:12,211Z", "level": "INFO", "component": "o.e.x.m.p.NativeController", "cluster.name": "docker-cluster", "node.name": "es-manual-89d68bb75-5k5l5", "message": "Native controller process has stopped - no new native processes can be started" }

```

```
kubectl edit deployment es-manual
```
If you have :
env:
- name: discovery.type
replace the dot

expect
```
 Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2023-01-09T11:28:55Z"
  generation: 1
  labels:
    app: es-manual
  name: es-manual
  namespace: default
  resourceVersion: "13481"
  uid: e9223323-08f0-42db-b253-e7dd9aeb9f84
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: es-manual
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: es-manual
    spec:
      containers:
      - image: elasticsearch:7.8.0
        imagePullPolicy: IfNotPresent
        name: elasticsearch
        resources: {}
# to exit
:q!
```

Changes made will result in a redeployment
```
kubectl get pods
```

```
NAME                         READY   STATUS        RESTARTS   AGE
es-manual-7d8bc4cf88-b2qr9   1/1     Running       0           7s
es-manual-d64d94fbc-dwwgz    0/1     Terminating   8           21m
```

```
kubectl expose deployment es-manual --type NodePort --port 9200
```

expect:
```
service/es-manual exposed
```

```
kubectl get services
```

expect:
```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)
kubernetes   ClusterIP  10.96.0.1               443/TCP
es-manual    NodePort    10.43.84.214   <none>        9200:32013/TCP   32
```

```
# the SERVER IP IS: 10.154.2.93
curl http://10.154.2.93:32013

```

## Deploying Kibana
```
kubectl create deployment kib-manual --image kibana:7.8.0
```

expect:
```
deployment.apps/kib-manual created
```

```
kubectl get pods
```

expect:
```
NAME                         READY   STATUS    RESTARTS   AGE
es-manual-7d8bc4cf88-b2qr9   1/1     Running   2          3d1h
kib-manual-6fc75c577f-dhvwq  1/1     Running   0          3m32s
```

```
kubectl logs pods/kib-manual-6fc75c577f-dhvwq
```

expect
```
{"type":"log","@timestamp":"2023-01-09T12:00:16Z","tags":["warning","plugins-discovery"],"pid":7,"message":"Expect plugin \"id\" in camelCase, but found: apm_oss"}
{"type":"log","@timestamp":"2023-01-09T12:00:16Z","tags":["warning","plugins-discovery"],"pid":7,"message":"Expect plugin \"id\" in camelCase, but found: triggers_actions_ui"}
{"type":"log","@timestamp":"2023-01-09T12:00:56Z","tags":["info","plugins-service"],"pid":7,"message":"Plugin \"visTypeXy\" is disabled."}
{"type":"log","@timestamp":"2023-01-09T12:00:56Z","tags":["info","plugins-service"],"pid":7,"message":"Plugin \"endpoint\" is disabled."}
{"type":"log","@timestamp":"2023-01-09T12:00:56Z","tags":["info","plugins-service"],"pid":7,"message":"Plugin \"ingestManager\" is disabled."}
{"type":"log","@timestamp":"2023-01-09T12:00:56Z","tags":["info","plugins-service"],"pid":7,"message":"Plugin \"lists\" is disabled."}

```

```
kubectl set env deployments/kib-manual ELASTICSEARCH_HOSTS=http://10.154.2.93:30445
```
expect :
```
deployment.apps/kib-manual env updated
```

```
 kubectl logs –f pods/kib-manual-7c7f848654-z5f9c
```

expect:
```
{"type":"log","@timestamp":"2020-07-17T14:45:41Z","tags":["listening","info"],"pid":6,"message":"Server running at http://0:5601"}
{"type":"log","@timestamp":"2020-07-17T14:45:41Z","tags":["info","http","server","Kibana"],"pid":6,"message":"http server running at http://0:5601"}
```

```
kubectl expose deployment kib-manual --type NodePort --port 5601
```

expect
```
service/kib-manual exposed
```

```
kubectl get services
```

expect:
```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP   10.43.0.1       <none>        443/TCP          2d
es-manual    NodePort    10.43.84.214    <none>        9200:32013/TCP   19m
kib-manual   NodePort    10.43.162.110   <none>        5601:31904/TCP   27s
```
Open browser to :
http://10.154.2.93:31904/

## Deploying Logstash

```
nano logstash.conf
```
Put this content
```
input {
  beats {
    port => "5044"
  }
}
 
output {
  elasticsearch {
    hosts => ["http://10.154.2.93:30445"]
    index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"
  }
  stdout {
    codec => rubydebug
  }
}
```

create a new file called deployment.yml
```
nano deployment.yml
```

```
---
kind: Deployment
apiVersion: apps/v1
metadata:
  name: log-manual
spec:
  replicas: 1
  selector:
    matchLabels:
      name: log-manual
  template:
    metadata:
      labels:
        name: log-manual
    spec:
      hostname: log-manual
  containers:
   - name: log-manual
     ports:
     - containerPort: 5044
       name: filebeat
       image: logstash:7.8.0
     volumeMounts:
     - name: log-manual-pipeline
       mountPath: /usr/share/logstash/pipeline/
     command:
     - logstash
     volumes:
      - name: log-manual-pipeline
     configMap:
       name: log-manual-pipeline
       items:
       - key: logstash.conf
         path: logstash.conf
---
```

```
kubectl create configmap log-manual-pipeline --from-file ./logstash.conf
```

expect
```
configmap/log-manual-pipeline created
```

```
kubectl create –f ./deployment.yml
```

```
deployment.apps/log-manual created
```

```
kubectl get pods
```

expect
```
NAME                          READY   STATUS    RESTARTS   AGE
es-manual-7d8bc4cf88-b2qr9    1/1     Running   3          7d2h
kib-manual-7c7f848654-z5f9c   1/1     Running   1          3d23h
log-manual-5c95bd7497-ldblg   1/1     Running   0          4s
```

```
kubectl logs –f log-manual-5c95bd7497-ldblg
```
expect
```
...
... Beats inputs: Starting input listener {:address=>"0.0.0.0:5044"}
... Pipeline started {"pipeline.id"=>"main"}
... Pipelines running {:count=>1, :running_pipelines=>[:main], :non_running_pipelines=>[]}
... Starting server on port: 5044
... Successfully started Logstash API endpoint {:port=>9600}
```

```
kubectl expose deployment log-manual --type NodePort --port 5044
```
expect
```
service/log-manual exposed
```


```
 kubectl get services
```

expect
```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)
es-manual    NodePort    10.96.114.186           9200:30445/TCP
kib-manual   NodePort    10.96.112.148           5601:31112/TCP
kubernetes   ClusterIP   10.96.0.1               443/TCP
log-manual   NodePort    10.96.254.84            5044:31010/TCP
```

Create a file named filebeat.yml 
```
filebeat.inputs:
 - type: log
   paths:
    - /tmp/output.log
 
output:
  logstash:
    hosts: [ "192.168.99.102:31010" ]
```


```
kubectl create configmap beat-manual-config --from-file ./filebeat.yml
```

expect:
```
configmap/beat-manual-config created
```

 create a deployment.yml 
```
kind: Deployment
  apiVersion: apps/v1
  metadata:
    name: logging-app-manual
  spec:
    replicas: 1
    selector:
      matchLabels:
        name: logging-app-manual
  template:
    metadata:
      labels:
        name: logging-app-manual
    spec:
      hostname: logging-app-manual
      containers:
        - name: beat-manual
          image: elastic/filebeat:7.8.0
          args: [
            "-c", "/etc/filebeat/filebeat.yml",
            "-e"
          ]
          volumeMounts:
            - name: beat-manual-config
              mountPath: /etc/filebeat/
            - name: manual-log-directory
              mountPath: /tmp/
        - name: logging-app-manual
          image: sladesoftware/log-application:latest
          volumeMounts:
            - name: manual-log-directory
              mountPath: /tmp/
      volumes:
        - name: beat-manual-config
          configMap:
            name: beat-manual-config
            items:
              - key: filebeat.yml
                path: filebeat.yml
        - name: manual-log-directory
          emptyDir: {}
```

```
kubectl create –f ./deployment.yml
```

expect
```
deployment.apps/logging-app-manual created
```












### Bibliography

https://coralogix.com/blog/elasticsearch-logstash-kibana-on-kubernetes/
