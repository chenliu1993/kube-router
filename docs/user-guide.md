# User Guide

## Try Kube-router with cluster installers

The best way to get started is to deploy Kubernetes with Kube-router is with a cluster installer.

### kops

Please see the [steps](https://github.com/cloudnativelabs/kube-router/blob/master/docs/kops.md) to deploy Kubernetes
cluster with Kube-router using [Kops](https://github.com/kubernetes/kops)

### kubeadm

Please see the [steps](https://github.com/cloudnativelabs/kube-router/blob/master/docs/kubeadm.md) to deploy Kubernetes
cluster with Kube-router using [Kubeadm](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/)

### k0sproject

k0s by default uses kube-router as a CNI option.
Please see the [steps](https://docs.k0sproject.io/latest/install/) to deploy Kubernetes cluster with Kube-router using
[k0s](https://docs.k0sproject.io/)

### k3sproject

[k3s](https://k3s.io/) by default uses
[kube-router's network policy controller implementation](https://docs.k3s.io/networking#network-policy-controller) for
its NetworkPolicy enforcement.

### generic

Please see the [steps](https://github.com/cloudnativelabs/kube-router/blob/master/docs/generic.md) to deploy kube-router
on manually installed clusters

### Amazon specific notes

When running in an AWS environment that requires an explicit proxy you need to inject the proxy server as a
[environment variable](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)
in your kube-router deployment

Example:

```yaml
env:
- name: HTTP_PROXY
  value: "http://proxy.example.com:80"
```

### Azure specific notes

Azure does not support IPIP packet encapsulation which is the default packet encapsulation that kube-router uses. If you
need to use an overlay network in an Azure environment with kube-router, please ensure that you set
`--overlay-encap=fou`. See [kube-router Tunnel Documentation](tunnels.md) for more information.

## deployment

Depending on what functionality of kube-router you want to use, multiple deployment options are possible. You can use
the flags `--run-firewall`, `--run-router`, `--run-service-proxy`, `--run-loadbalancer` to selectively enable only
required functionality of kube-router.

Also you can choose to run kube-router as agent running on each cluster node. Alternativley you can run kube-router as
pod on each node through daemonset.

## command line options

```sh
Usage of kube-router:
      --advertise-cluster-ip                          Add Cluster IP of the service to the RIB so that it gets advertises to the BGP peers.
      --advertise-external-ip                         Add External IP of service to the RIB so that it gets advertised to the BGP peers.
      --advertise-loadbalancer-ip                     Add LoadbBalancer IP of service status as set by the LB provider to the RIB so that it gets advertised to the BGP peers.
      --advertise-pod-cidr                            Add Node's POD cidr to the RIB so that it gets advertised to the BGP peers. (default true)
      --auto-mtu                                      Auto detect and set the largest possible MTU for kube-bridge and pod interfaces (also accounts for IPIP overlay network when enabled). (default true)
      --bgp-graceful-restart                          Enables the BGP Graceful Restart capability so that routes are preserved on unexpected restarts
      --bgp-graceful-restart-deferral-time duration   BGP Graceful restart deferral time according to RFC4724 4.1, maximum 18h. (default 6m0s)
      --bgp-graceful-restart-time duration            BGP Graceful restart time according to RFC4724 3, maximum 4095s. (default 1m30s)
      --bgp-holdtime duration                         This parameter is mainly used to modify the holdtime declared to BGP peer. When Kube-router goes down abnormally, the local saving time of BGP route will be affected. Holdtime must be in the range 3s to 18h12m16s. (default 1m30s)
      --bgp-port uint32                               The port open for incoming BGP connections and to use for connecting with other BGP peers. (default 179)
      --cache-sync-timeout duration                   The timeout for cache synchronization (e.g. '5s', '1m'). Must be greater than 0. (default 1m0s)
      --cleanup-config                                Cleanup iptables rules, ipvs, ipset configuration and exit.
      --cluster-asn uint                              ASN number under which cluster nodes will run iBGP.
      --disable-source-dest-check                     Disable the source-dest-check attribute for AWS EC2 instances. When this option is false, it must be set some other way. (default true)
      --enable-cni                                    Enable CNI plugin. Disable if you want to use kube-router features alongside another CNI plugin. (default true)
      --enable-ibgp                                   Enables peering with nodes with the same ASN, if disabled will only peer with external BGP peers (default true)
      --enable-ipv4                                   Enables IPv4 support (default true)
      --enable-ipv6                                   Enables IPv6 support
      --enable-overlay                                When enable-overlay is set to true, IP-in-IP tunneling is used for pod-to-pod networking across nodes in different subnets. When set to false no tunneling is used and routing infrastructure is expected to route traffic for pod-to-pod networking across nodes in different subnets (default true)
      --enable-pod-egress                             SNAT traffic from Pods to destinations outside the cluster. (default true)
      --enable-pprof                                  Enables pprof for debugging performance and memory leak issues.
      --excluded-cidrs strings                        Excluded CIDRs are used to exclude IPVS rules from deletion.
      --gobgp-admin-port uint16                       Port to connect to GoBGP for administrative purposes. Setting this to 0 will disable the GoBGP gRPC server. (default 50051)
      --hairpin-mode                                  Add iptables rules for every Service Endpoint to support hairpin traffic.
      --health-port uint16                            Health check port, 0 = Disabled (default 20244)
  -h, --help                                          Print usage information.
      --hostname-override string                      Overrides the NodeName of the node. Set this if kube-router is unable to determine your NodeName automatically.
      --injected-routes-sync-period duration          The delay between route table synchronizations  (e.g. '5s', '1m', '2h22m'). Must be greater than 0. (default 1m0s)
      --iptables-sync-period duration                 The delay between iptables rule synchronizations (e.g. '5s', '1m'). Must be greater than 0. (default 5m0s)
      --ipvs-graceful-period duration                 The graceful period before removing destinations from IPVS services (e.g. '5s', '1m', '2h22m'). Must be greater than 0. (default 30s)
      --ipvs-graceful-termination                     Enables the experimental IPVS graceful terminaton capability
      --ipvs-permit-all                               Enables rule to accept all incoming traffic to service VIP's on the node. (default true)
      --ipvs-sync-period duration                     The delay between ipvs config synchronizations (e.g. '5s', '1m', '2h22m'). Must be greater than 0. (default 5m0s)
      --kubeconfig string                             Path to kubeconfig file with authorization information (the master location is set by the master flag).
      --loadbalancer-default-class                    Handle loadbalancer services without a class (default true)
      --loadbalancer-ip-range strings                 CIDR values from which loadbalancer services addresses are assigned (can be specified multiple times)
      --loadbalancer-sync-period duration             The delay between checking for missed services (e.g. '5s', '1m'). Must be greater than 0. (default 1m0s)
      --masquerade-all                                SNAT all traffic to cluster IP/node port.
      --master string                                 The address of the Kubernetes API server (overrides any value in kubeconfig).
      --metrics-addr string                           Prometheus metrics address to listen on, (Default: all interfaces)
      --metrics-path string                           Prometheus metrics path (default "/metrics")
      --metrics-port uint16                           Prometheus metrics port, (Default 0, Disabled)
      --nodeport-bindon-all-ip                        For service of NodePort type create IPVS service that listens on all IP's of the node.
      --nodes-full-mesh                               Each node in the cluster will setup BGP peering with rest of the nodes. (default true)
      --overlay-encap string                          Valid encapsulation types are "ipip" or "fou" (if set to "fou", the udp port can be specified via "overlay-encap-port") (default "ipip")
      --overlay-encap-port uint16                     Overlay tunnel encapsulation port (only used for "fou" encapsulation) (default 5555)
      --overlay-type string                           Possible values: subnet,full - When set to "subnet", the default, default "--enable-overlay=true" behavior is used. When set to "full", it changes "--enable-overlay=true" default behavior so that IP-in-IP tunneling is used for pod-to-pod networking across nodes regardless of the subnet the nodes are in. (default "subnet")
      --override-nexthop                              Override the next-hop in bgp routes sent to peers with the local ip.
      --peer-router-asns uints                        ASN numbers of the BGP peer to which cluster nodes will advertise cluster ip and node's pod cidr. (default [])
      --peer-router-ips ipSlice                       The ip address of the external router to which all nodes will peer and advertise the cluster ip and pod cidr's. (default [])
      --peer-router-multihop-ttl uint8                Enable eBGP multihop supports -- sets multihop-ttl. (Relevant only if ttl >= 2)
      --peer-router-passwords strings                 Password for authenticating against the BGP peer defined with "--peer-router-ips".
      --peer-router-passwords-file string             Path to file containing password for authenticating against the BGP peer defined with "--peer-router-ips". --peer-router-passwords will be preferred if both are set.
      --peer-router-ports uints                       The remote port of the external BGP to which all nodes will peer. If not set, default BGP port (179) will be used. (default [])
      --router-id string                              BGP router-id. Must be specified in a ipv6 only cluster, "generate" can be specified to generate the router id.
      --routes-sync-period duration                   The delay between route updates and advertisements (e.g. '5s', '1m', '2h22m'). Must be greater than 0. (default 5m0s)
      --run-firewall                                  Enables Network Policy -- sets up iptables to provide ingress firewall for pods. (default true)
      --run-loadbalancer                              Enable loadbalancer address allocator
      --run-router                                    Enables Pod Networking -- Advertises and learns the routes to Pods via iBGP. (default true)
      --run-service-proxy                             Enables Service Proxy -- sets up IPVS for Kubernetes Services. (default true)
      --runtime-endpoint string                       Path to CRI compatible container runtime socket (used for DSR mode). Currently known working with containerd.
      --service-cluster-ip-range strings              CIDR values from which service cluster IPs are assigned (can be specified up to 2 times) (default [10.96.0.0/12])
      --service-external-ip-range strings             Specify external IP CIDRs that are used for inter-cluster communication (can be specified multiple times)
      --service-node-port-range string                NodePort range specified with either a hyphen or colon (default "30000-32767")
      --service-tcp-timeout duration                  Specify TCP timeout for IPVS services in standard duration syntax (e.g. '5s', '1m'), default 0s preserves default system value (default: 0s)
      --service-tcpfin-timeout duration               Specify TCP FIN timeout for IPVS services in standard duration syntax (e.g. '5s', '1m'), default 0s preserves default system value (default: 0s)
      --service-udp-timeout duration                  Specify UDP timeout for IPVS services in standard duration syntax (e.g. '5s', '1m'), default 0s preserves default system value (default: 0s)
  -v, --v string                                      log level for V logs (default "0")
  -V, --version                                       Print version information.
```

## requirements

- Kube-router need to access kubernetes API server to get information on pods, services, endpoints, network policies
  etc. The very minimum information it requires is the details on where to access the kubernetes API server. This
  information can be passed as:

```sh
kube-router --master=http://192.168.1.99:8080/` or `kube-router --kubeconfig=<path to kubeconfig file>
```

- If you run kube-router as agent on the node, ipset package must be installed on each of the nodes (when run as
  daemonset, container image is prepackaged with ipset)

- If you choose to use kube-router for pod-to-pod network connectivity then Kubernetes controller manager need to be
  configured to allocate pod CIDRs by passing `--allocate-node-cidrs=true` flag and providing a `cluster-cidr` (i.e. by
  passing --cluster-cidr=10.1.0.0/16 for e.g.)

- If you choose to run kube-router as daemonset in Kubernetes version below v1.15, both kube-apiserver and kubelet must
  be run with `--allow-privileged=true` option. In later Kubernetes versions, only kube-apiserver must be run with
  `--allow-privileged=true` option and if PodSecurityPolicy admission controller is enabled, you should create
  PodSecurityPolicy, allowing privileged kube-router pods.
  - Additionally, when run in daemonset mode, it is highly recommended that you keep netfilter related userspace host
    tooling like `iptables`, `ipset`, and `ipvsadm` in sync with the versions that are distributed by Alpine inside the
    kube-router container. This will help avoid conflicts that can potentially arise when both the host's userspace and
    kube-router's userspace tooling modifies netfilter kernel definitions. See:
    [this kube-router issue](https://github.com/cloudnativelabs/kube-router/issues/1370) for more information.

- If you choose to use kube-router for pod-to-pod network connecitvity then Kubernetes cluster must be configured to use
  CNI network plugins. On each node CNI conf file is expected to be present as /etc/cni/net.d/10-kuberouter.conf
  `bridge` CNI plugin and `host-local` for IPAM should be used. A sample conf file that can be downloaded as

```sh
wget -O /etc/cni/net.d/10-kuberouter.conf https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/cni/10-kuberouter.conf`
```

- Additionally, the aforementioned `bridge` and `host-local` CNI plugins need to exist for the container runtime to
  reference if you have kube-router manage the pod-to-pod network. Additionally, if you use `hostPort`'s on any of your
  pods, you'll need to install the `hostport` plugin. As of kube-router v2.1.X, these plugins will be installed to
  `/opt/cni/bin` for you during the `initContainer` phase if kube-router finds them missing. Most container runtimes
  will know to look for your plugins there by default, however, you may have to configure them if you are having
  problems with your pods coming up.
  - [containerd configuration](https://github.com/containerd/containerd/blob/c1d59e38ef222f5a80dde9d817bac1f98e2db78c/docs/cri/config.md?plain=1#L409)
  - [cri-o configuration](https://github.com/cri-o/cri-o/blob/main/contrib/cni/README.md#plugin-directory)
  - [cri-dockerd configuration](https://github.com/Mirantis/cri-dockerd/blob/519e39ceaa7f9e00319149b9d74b243466fa3963/config/options.go#L161)

## running as daemonset

This is quickest way to deploy kube-router in Kubernetes (**dont forget to ensure the requirements above**).
Just run:

```sh
kubectl apply -f https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/daemonset/kube-router-all-service-daemonset.yaml
```

Above will run kube-router as pod on each node automatically. You can change the arguments in the daemonset definition
as required to suit your needs. Some sample deployment configuration can be found
[in our daemonset examples](https://github.com/cloudnativelabs/kube-router/tree/master/daemonset) with different
arguments used to select a set of the services kube-router should run.

## running as agent

You can choose to run kube-router as agent runnng on each node. For e.g if you just want kube-router to provide ingress
firewall for the pods then you can start kube-router as:

```sh
kube-router --master=http://192.168.1.99:8080/ --run-firewall=true --run-service-proxy=false --run-router=false
```

## cleanup configuration

Please delete kube-router daemonset and then clean up all the configurations done (to ipvs, iptables, ipset, ip routes
etc) by kube-router on the node by running below command.

### Docker

```sh
docker run --privileged --net=host \
--mount type=bind,source=/lib/modules,target=/lib/modules,readonly \
--mount type=bind,source=/run/xtables.lock,target=/run/xtables.lock,bind-propagation=rshared \
cloudnativelabs/kube-router /usr/local/bin/kube-router --cleanup-config
```

### containerd

```sh
$ ctr image pull docker.io/cloudnativelabs/kube-router:latest
$ ctr run --privileged -t --net-host \
--mount type=bind,src=/lib/modules,dst=/lib/modules,options=rbind:ro \
--mount type=bind,src=/run/xtables.lock,dst=/run/xtables.lock,options=rbind:rw \
docker.io/cloudnativelabs/kube-router:latest kube-router-cleanup /usr/local/bin/kube-router --cleanup-config
```

## trying kube-router as alternative to kube-proxy

If you have a kube-proxy in use, and want to try kube-router just for service proxy you can do

```sh
kube-proxy --cleanup-iptables
```

followed by

```sh
kube-router --master=http://192.168.1.99:8080/ --run-service-proxy=true --run-firewall=false --run-router=false
```

and if you want to move back to kube-proxy then clean up config done by kube-router by running

```sh
 kube-router --cleanup-config
```

and run kube-proxy with the configuration you have.

## Advertising IPs

kube-router can advertise Cluster, External and LoadBalancer IPs to BGP peers.
It does this by:

- locally adding the advertised IPs to the nodes' `kube-dummy-if` network interface
- advertising the IPs to its BGP peers

To set the default for all services use the `--advertise-cluster-ip`, `--advertise-external-ip` and
`--advertise-loadbalancer-ip` flags.

To selectively enable or disable this feature per-service use the `kube-router.io/service.advertise.clusterip`,
`kube-router.io/service.advertise.externalip` and `kube-router.io/service.advertise.loadbalancerip` annotations.

e.g.:
`$ kubectl annotate service my-advertised-service "kube-router.io/service.advertise.clusterip=true"`
`$ kubectl annotate service my-advertised-service "kube-router.io/service.advertise.externalip=true"`
`$ kubectl annotate service my-advertised-service "kube-router.io/service.advertise.loadbalancerip=true"`

`$ kubectl annotate service my-non-advertised-service "kube-router.io/service.advertise.clusterip=false"`
`$ kubectl annotate service my-non-advertised-service "kube-router.io/service.advertise.externalip=false"`
`$ kubectl annotate service my-non-advertised-service "kube-router.io/service.advertise.loadbalancerip=false"`

By combining the flags with the per-service annotations you can choose either a opt-in or opt-out strategy for
advertising IPs.

Advertising LoadBalancer IPs works by inspecting the services `status.loadBalancer.ingress` IPs that are set by external
LoadBalancers like for example MetalLb. This has been successfully tested together with
[MetalLB](https://github.com/google/metallb) in ARP mode.

## Controlling Service Locality / Traffic Policies

Service availability both externally and locally (within the cluster) can be controlled via the Kubernetes standard
[Traffic Policies](https://kubernetes.io/docs/reference/networking/virtual-ips/#traffic-policies) and via the custom
kube-router service annotation: `kube-router.io/service.local: true`.

Refer to the previously linked upstream Kubernetes documentation for more information on `spec.internalTrafficPolicy`
and `spec.externalTrafficPolicy`.

In order to keep backwards compatibility the `kube-router.io/service.local: true` annotation effectively overrides
`spec.internalTrafficPolicy` and `spec.externalTrafficPolicy` and forces kube-router to behave as if both were set to
`Local`.

## Hairpin Mode

Communication from a Pod that is behind a Service to its own ClusterIP:Port is not supported by default.  However, it
can be enabled per-service by adding the `kube-router.io/service.hairpin=` annotation, or for all Services in a cluster by
passing the flag `--hairpin-mode=true` to kube-router.

Additionally, the `hairpin_mode` sysctl option must be set to `1` for all veth interfaces on each node.  This can be
done by adding the `"hairpinMode": true` option to your CNI configuration and rebooting all cluster nodes if they are
already running kubernetes.

Hairpin traffic will be seen by the pod it originated from as coming from the Service ClusterIP if it is logging the
source IP.

### Hairpin Mode Example

10-kuberouter.conf

```json
{
    "name":"mynet",
    "type":"bridge",
    "bridge":"kube-bridge",
    "isDefaultGateway":true,
    "hairpinMode":true,
    "ipam": {
        "type":"host-local"
     }
}
```

To enable hairpin traffic for Service `my-service`:

```sh
kubectl annotate service my-service "kube-router.io/service.hairpin="
```

If you want to also hairpin externalIPs declared for Service `my-service` (note, you must also either enable global
hairpin or service hairpin (see above ^^^)  for this to have an effect):

```sh
kubectl annotate service my-service "kube-router.io/service.hairpin.externalips="
```

## SNATing Service Traffic

By default, as traffic ingresses into the cluster, kube-router will source nat the traffic to ensure symmetric routing
if it needs to proxy that traffic to ensure it gets to a node that has a service pod that is capable of servicing the
traffic. This has a potential to cause issues when network policies are applied to that service since now the traffic
will appear to be coming from a node in your cluster instead of the traffic originator.

This is an issue that is common to all proxy's and all Kubernetes service proxies in general. You can read more
information about this issue at:
[Source IP for Services](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-nodeport)

In addition to the fix mentioned in the linked upstream documentation (using `service.spec.externalTrafficPolicy`),
kube-router also provides [DSR](dsr.md), which by its nature preserves the source IP, to solve this problem. For more
information see the section above.

## Load balancing Scheduling Algorithms

Kube-router uses LVS for service proxy. LVS supports a rich set of [scheduling
algorithms](https://en.wikipedia.org/wiki/Linux_Virtual_Server#Schedulers). The
scheduling algorithm for a service is configured by means of annotations. The
`round-robin` scheduler is used by default when a service is lacks the
scheduler annotation.

```sh
#For least connection scheduling use:
$ kubectl annotate service my-service "kube-router.io/service.scheduler=lc"

#For round-robin scheduling use:
$ kubectl annotate service my-service "kube-router.io/service.scheduler=rr"

#For source hashing scheduling use:
$ kubectl annotate service my-service "kube-router.io/service.scheduler=sh"

#For destination hashing scheduling use:
$ kubectl annotate service my-service "kube-router.io/service.scheduler=dh"

#For maglev scheduling use:
$ kubectl annotate service my-service "kube-router.io/service.scheduler=mh"

# The maglev scheduler can be further tuned with additional options.
#To use the maglev scheduler's fallback option use:
$ kubectl annotate service my-service "kube-router.io/service.schedflags=flag-1"
#To use the maglev scheduler's port option use:
$ kubectl annotate service my-service "kube-router.io/service.schedflags=flag-2"
#To use the maglev scheduler's port and fallback option use:
$ kubectl annotate service my-service "kube-router.io/service.schedflags=flag-1,flag-2"
```

## HostPort support

If you would like to use `HostPort` functionality below changes are required in the manifest.

- By default kube-router assumes CNI conf file to be `/etc/cni/net.d/10-kuberouter.conf`. Add an environment variable
`KUBE_ROUTER_CNI_CONF_FILE` to kube-router manifest and set it to `/etc/cni/net.d/10-kuberouter.conflist`

- Modify `kube-router-cfg` ConfigMap with CNI config that supports `portmap` as additional plug-in

```json
    {
       "cniVersion":"0.3.0",
       "name":"mynet",
       "plugins":[
          {
             "name":"kubernetes",
             "type":"bridge",
             "bridge":"kube-bridge",
             "isDefaultGateway":true,
             "ipam":{
                "type":"host-local"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "snat":true,
                "portMappings":true
             }
          }
       ]
    }
```

- Update init container command to create `/etc/cni/net.d/10-kuberouter.conflist` file
- Restart the container runtime

For an e.g manifest please look at [manifest](../daemonset/kubeadm-kuberouter-all-features-hostport.yaml) with necessary
changes required for `HostPort` functionality.

## IPVS Graceful termination support

As of 0.2.6 we support experimental graceful termination of IPVS destinations. When possible the pods's
TerminationGracePeriodSeconds is used, if it cannot be retrived for some reason the fallback period is 30 seconds and
can be adjusted with `--ipvs-graceful-period` cli-opt

graceful termination works in such a way that when kube-router receives a delete endpoint notification for a service
it's weight is adjusted to 0 before getting deleted after he termination grace period has passed or the Active &
Inactive connections goes down to 0.

## MTU

The maximum transmission unit (MTU) determines the largest packet size that can be transmitted through your network. MTU
for the pod interfaces should be set appropriately to prevent fragmentation and packet drops thereby achieving maximum
performance. If `auto-mtu` is set to true (`auto-mtu` is set to true by default as of kube-router 1.1), kube-router will
determine right MTU for both `kube-bridge` and pod interfaces. If you set `auto-mtu` to false kube-router will not
attempt to configure MTU. However you can choose the right MTU and set in the `cni-conf.json` section of the
`10-kuberouter.conflist` in the kube-router [daemonsets](../daemonset/). For e.g.

```json
  cni-conf.json: |
    {
       "cniVersion":"0.3.0",
       "name":"mynet",
       "plugins":[
          {
             "name":"kubernetes",
             "type":"bridge",
             "mtu": 1400,
             "bridge":"kube-bridge",
             "isDefaultGateway":true,
             "ipam":{
                "type":"host-local"
             }
          }
       ]
    }
```

 If you set MTU yourself via the CNI config, you'll also need to set MTU of `kube-bridge` manually to the right value
 to avoid packet fragmentation in case of existing nodes on which `kube-bridge` is already created. On node reboot or

in case of new nodes joining the cluster both the pod's interface and `kube-bridge` will be setup with specified MTU value.

## BGP configuration

[Configuring BGP Peers](bgp.md)

## Metrics

[Configure metrics gathering](metrics.md)
