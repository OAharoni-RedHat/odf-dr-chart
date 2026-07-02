# odf-dr-chart

![Version: 0.0.1](https://img.shields.io/badge/Version-0.0.1-informational?style=flat-square)

Standalone Helm chart for ODF storage infrastructure supporting Regional DR. Deploys ODF SSL certificate extraction, Submariner network overlay, MirrorPeer storage mirroring, ODF DR prerequisites.

This chart deploys ODF storage infrastructure supporting Regional DR: SSL certificate extraction, Submariner network overlay, MirrorPeer storage mirroring, ODF DR prerequisites, and Ramen hub trusted-CA workloads.
Consumed by the ramendr pattern as a dedicated ArgoCD application.

## Notable changes

v0.1.0 - Initial release

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| ansible.configMapArgoSyncOptions | string | `"Prune=false,ServerSideApply=true"` | Argo CD resource sync-options applied to the Ansible ConfigMap. |
| ansible.containerImage | string | `"quay.io/validatedpatterns/utility-container:latest"` | Container image used for Ansible post-install jobs. |
| ansible.verbosity | int | `0` | Ansible-playbook verbosity level (0–4). |
| clusterCaMgt.createNamespace | bool | `false` | Create clusterCaMgt.namespace when installing the chart. |
| clusterCaMgt.namespace | string | `"cluster-ca-mgt"` | Namespace for ODF CA prerequisites and Ramen trusted-CA workloads. |
| global.clusterDomain | string | `"cluster.example.com"` | Base domain shared by all clusters (e.g. example.com). Used to derive per-cluster baseDomain. |
| global.clusterPlatform | string | `"AWS"` | Cloud platform type. AWS enables Hive ExternalSecret, ClusterDeployment platform.aws, Submariner gateway/credentials and SG-tag job. Use non-AWS (e.g. BareMetal) to skip those. |
| odf.drCluster.primaryS3ProfileName | string | `""` | S3 profile name for the primary DRCluster CR. Required only when postInstallFixesEnabled is false. |
| odf.drCluster.secondaryS3ProfileName | string | `""` | S3 profile name for the secondary DRCluster CR. Required only when postInstallFixesEnabled is false. |
| odf.postInstallFixesEnabled | bool | `true` | Enable ODF post-install automation (MirrorPeer, prerequisites checker, Ramen trusted-CA jobs/RBAC). |
| odfRamenTrustedCa.pollInterval | int | `15` | Polling interval in seconds for readiness checks inside the trusted-CA job. |
| odfRamenTrustedCa.ramenS3WaitSeconds | int | `3600` | Maximum seconds to wait for Ramen s3StoreProfiles to be populated before the trusted-CA job gives up. |
| odfRamenTrustedCa.trustedCaWaitSeconds | int | `3600` | Maximum seconds to wait for the hub cluster-proxy-ca-bundle trusted CA before the job gives up. |
| odfSslCertificateExtractor.clusterReadinessMaxAttempts | int | `150` | Maximum number of attempts to check DR ManagedCluster readiness before the extractor job fails. |
| odfSslCertificateExtractor.clusterReadinessSleepSeconds | int | `30` | Seconds to sleep between each ManagedCluster readiness poll attempt. |
| regionalDR[0].clusters.primary.clusterGroup | string | `"resilient"` | Cluster group label for the primary cluster. |
| regionalDR[0].clusters.primary.install_config.apiVersion | string | `"v1"` |  |
| regionalDR[0].clusters.primary.install_config.baseDomain | string | `"{{ join \".\" (slice (splitList \".\" $.Values.global.clusterDomain) 1) }}"` |  |
| regionalDR[0].clusters.primary.install_config.compute[0].name | string | `"worker"` |  |
| regionalDR[0].clusters.primary.install_config.compute[0].platform.aws.type | string | `"m5.metal"` |  |
| regionalDR[0].clusters.primary.install_config.compute[0].replicas | int | `3` |  |
| regionalDR[0].clusters.primary.install_config.controlPlane.name | string | `"master"` |  |
| regionalDR[0].clusters.primary.install_config.controlPlane.platform.aws.type | string | `"m5.4xlarge"` |  |
| regionalDR[0].clusters.primary.install_config.controlPlane.replicas | int | `3` |  |
| regionalDR[0].clusters.primary.install_config.metadata.name | string | `"ocp-primary"` |  |
| regionalDR[0].clusters.primary.install_config.networking.clusterNetwork[0].cidr | string | `"10.132.0.0/14"` |  |
| regionalDR[0].clusters.primary.install_config.networking.clusterNetwork[0].hostPrefix | int | `23` |  |
| regionalDR[0].clusters.primary.install_config.networking.machineNetwork[0].cidr | string | `"10.1.0.0/16"` |  |
| regionalDR[0].clusters.primary.install_config.networking.networkType | string | `"OVNKubernetes"` |  |
| regionalDR[0].clusters.primary.install_config.networking.serviceNetwork[0] | string | `"172.20.0.0/16"` |  |
| regionalDR[0].clusters.primary.install_config.platform.aws.region | string | `"us-west-1"` | AWS region for the primary cluster. |
| regionalDR[0].clusters.primary.install_config.platform.aws.userTags.project | string | `"ValidatedPatterns"` |  |
| regionalDR[0].clusters.primary.install_config.publish | string | `"External"` |  |
| regionalDR[0].clusters.primary.install_config.pullSecret | string | `""` | OpenShift pull secret (base64-encoded JSON). Leave empty to inherit from hub. |
| regionalDR[0].clusters.primary.install_config.sshKey | string | `""` | SSH public key injected into cluster nodes. Leave empty to use the hub's default. |
| regionalDR[0].clusters.primary.name | string | `"ocp-primary"` | ACM ManagedCluster metadata.name for the primary cluster. |
| regionalDR[0].clusters.primary.version | string | `"4.18.7"` | OCP version for the primary cluster (used by Hive ClusterDeployment). |
| regionalDR[0].clusters.secondary.clusterGroup | string | `"resilient"` | Cluster group label for the secondary cluster. |
| regionalDR[0].clusters.secondary.install_config.apiVersion | string | `"v1"` |  |
| regionalDR[0].clusters.secondary.install_config.baseDomain | string | `"{{ join \".\" (slice (splitList \".\" $.Values.global.clusterDomain) 1) }}"` |  |
| regionalDR[0].clusters.secondary.install_config.compute[0].name | string | `"worker"` |  |
| regionalDR[0].clusters.secondary.install_config.compute[0].platform.aws.type | string | `"m5.metal"` |  |
| regionalDR[0].clusters.secondary.install_config.compute[0].replicas | int | `3` |  |
| regionalDR[0].clusters.secondary.install_config.controlPlane.name | string | `"master"` |  |
| regionalDR[0].clusters.secondary.install_config.controlPlane.platform.aws.type | string | `"m5.4xlarge"` |  |
| regionalDR[0].clusters.secondary.install_config.controlPlane.replicas | int | `3` |  |
| regionalDR[0].clusters.secondary.install_config.metadata.name | string | `"ocp-secondary"` |  |
| regionalDR[0].clusters.secondary.install_config.networking.clusterNetwork[0].cidr | string | `"10.136.0.0/14"` |  |
| regionalDR[0].clusters.secondary.install_config.networking.clusterNetwork[0].hostPrefix | int | `23` |  |
| regionalDR[0].clusters.secondary.install_config.networking.machineNetwork[0].cidr | string | `"10.2.0.0/16"` |  |
| regionalDR[0].clusters.secondary.install_config.networking.networkType | string | `"OVNKubernetes"` |  |
| regionalDR[0].clusters.secondary.install_config.networking.serviceNetwork[0] | string | `"172.21.0.0/16"` |  |
| regionalDR[0].clusters.secondary.install_config.platform.aws.region | string | `"us-east-1"` | AWS region for the secondary cluster. |
| regionalDR[0].clusters.secondary.install_config.platform.aws.userTags.project | string | `"ValidatedPatterns"` |  |
| regionalDR[0].clusters.secondary.install_config.publish | string | `"External"` |  |
| regionalDR[0].clusters.secondary.install_config.pullSecret | string | `""` | OpenShift pull secret (base64-encoded JSON). Leave empty to inherit from hub. |
| regionalDR[0].clusters.secondary.install_config.sshKey | string | `""` | SSH public key injected into cluster nodes. Leave empty to use the hub's default. |
| regionalDR[0].clusters.secondary.name | string | `"ocp-secondary"` | ACM ManagedCluster metadata.name for the secondary cluster. |
| regionalDR[0].clusters.secondary.version | string | `"4.18.7"` | OCP version for the secondary cluster (used by Hive ClusterDeployment). |
| regionalDR[0].globalnetEnabled | bool | `false` | Enable Submariner Globalnet. Required when primary and secondary cluster CIDRs overlap. |
| regionalDR[0].name | string | `"resilient"` | Name of this DR pair set. Must be unique within the regionalDR list and match the ACM policy placement label. |
| submariner.NATTEnable | bool | `true` | Enable NAT traversal (NAT-T) for Submariner IPsec tunnels. |
| submariner.cableDriver | string | `"vxlan"` | Submariner cable driver (vxlan or libreswan). |
| submariner.instanceType | string | `"m5.xlarge"` | EC2 instance type for Submariner gateway nodes. |
| submariner.ipsecNatPort | int | `4500` | IPsec NAT-T UDP port used by Submariner. |
| submariner.sgTagJobEnabled | bool | `false` | Enable EC2 security group tagging job. AWS only; requires global.clusterPlatform=AWS. |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.14.2](https://github.com/norwoodj/helm-docs/releases/v1.14.2)
