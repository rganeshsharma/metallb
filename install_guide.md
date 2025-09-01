# What IS Configurable in Values File of Bitnami Helm chart for Metallb

1. FRR (BGP Mode) Enable/Disable
```yaml
speaker:
  frr:
    enabled: false  # Set to true to enable BGP mode with FRR
```

2. Speaker Component (Required for both L2 and BGP)
```yaml
speaker:
  enabled: true  # Must be true for both L2 and BGP modes
```

3. Speaker Tolerations (Important for node placement)
```yaml
speaker:
  tolerations: 
  - key: node-role.kubernetes.io/master
    effect: NoSchedule
    operator: Exists
```

# What IS NOT Configurable in Values File

## The following must be configured after installation using CRDs:

❌ BGP Peers - Not in values file

❌ IP Address Pools - Not in values file

❌ L2Advertisements - Not in values file

❌ BGPAdvertisements - Not in values file



# Key Values File Settings for BGP vs L2

For L2 Mode (Default - most common):
```yaml
speaker:
  enabled: true      # Required
  frr:
    enabled: false   # L2 mode doesn't need FRR
```

For BGP Mode (Advanced):
```yaml
speaker:
  enabled: true      # Required
  frr:
    enabled: true    # Enable FRR for advanced BGP features
```

# Complete Example Values Files

Option 1: L2 Mode Values (Simple)MetalLB L2 Mode Values FileCode # MetalLB L2 Mode Configuration
# Save as: metallb-l2-values.yaml

# Global settings
```yaml
global:
  imageRegistry: ""
  imagePullSecrets: []

# Install CRDs
installCRDs: true

# Controller settings
controller:
  enabled: true
  resources:
    limits:
    Mapped out BGP and L2 configuration options in values file.1sThe user is looking for BGP/L2 configuration in the values file. I've shown them what IS configurable (mainly the FRR enabled/disabled setting) and created an L2 mode values file. Now I should create a BGP mode values file as well to show the difference.Option 2: BGP Mode Values (Advanced)MetalLB BGP Mode Values FileCode # MetalLB BGP Mode Configuration
# Save as: metallb-bgp-values.yaml
```

# Global settings
```yaml 
global:
  imageRegistry: ""
  imagePullSecrets: []

# Install CRDs
installCRDs: true

# Controller settings
controller:
  enabled: true
  resources:
    limits:
  Mapped workflow for values file and CRD configuration.1sNow I should provide the complete workflow showing how the values file and post-installation CRD configuration work together.Complete Installation Workflow
```

# Step 1: Install MetalLB with Values File
```bash
# For L2 Mode
helm install metallb metallb/metallb \
  --values metallb-l2-values.yaml \
  --namespace metallb-system \
  --create-namespace

# OR for BGP Mode
helm install metallb metallb/metallb \
  --values metallb-bgp-values.yaml \
  --namespace metallb-system \
  --create-namespace
```

# Note: If you get into issues while craeting IP Address Pool and L2 Advertisemnet disbale LivenessProbe and readinessProbe for both Controller Speaker and frr.

# Step 2: Configure Address Pools and Advertisements (Post-Installation)
For L2 Mode:MetalLB L2 Post-Installation ConfigurationCode 
# MetalLB L2 Mode Post-Installation Configuration
# Save as: metallb-l2-config.yaml
# Apply AFTER MetalLB installation
```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: default-pool
  namespace: metallb-system
spec:
  # Replace wiPlanned BGP post-installation configuration for the example.0sI should also create the BGP post-installation configuration to complete the example.For BGP Mode:MetalLB BGP Post-Installation ConfigurationCode # MetalLB BGP Mode Post-Installation Configuration
# Save as: metallb-bgp-config.yaml
# Apply AFTER MetalLB installation

apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: bgp-pool
  namespace: metallb-system
spec:
  # Replace withPondered configurable locations for BGP and L2 settings.1sNow I should summarize the key points about what's configurable where.Summary: What Goes Where
```


In Helm Values File:

✅ Component enablement (speaker.enabled, controller.enabled)

✅ FRR mode (speaker.frr.enabled)

✅ Resource limits and requests

✅ Node tolerations and affinity

✅ Security contexts and RBAC

✅ Image registry and versions

✅ Metrics enablement


In Post-Installation CRDs:

✅ IP Address Pools (IPAddressPool CRD)

✅ BGP Peers (BGPPeer CRD)

✅ L2 Advertisements (L2Advertisement CRD)

✅ BGP Advertisements (BGPAdvertisement CRD)


Key Difference from Other Helm Charts
Unlike many Helm charts where everything is configured in values.yaml, MetalLB follows a two-phase approach:

# Phase 1: Helm installs the components

# Phase 2: CRDs configure the behavior

This design allows for:

Dynamic reconfiguration without Helm upgrades

Multiple address pools and BGP peers

Environment-specific configurations

Better separation of concerns

Quick Start Command
```bash
# 1. Install MetalLB (choose L2 or BGP values)
helm install metallb metallb/metallb \
  --values metallb-l2-values.yaml \
  --namespace metallb-system \
  --create-namespace

# 2. Wait for ready
kubectl wait --for=condition=ready pod -l app.kubernetes.io/name=metallb -n metallb-system

# 3. Apply configuration
kubectl apply -f metallb-l2-config.yaml
```
# The main thing to remember: BGP/L2 behavior is NOT in the values file - it's configured afterward with CRDs!