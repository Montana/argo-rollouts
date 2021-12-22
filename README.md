![image](https://user-images.githubusercontent.com/20936398/147022386-55fc2c8b-92d5-4a34-908f-9d83285c1aef.png)


# Argo Rollout 

I've attached a Argo YAML file, to go along with this brief README I'll be putting together.


## Usage

These are the steps needed to deploy an Argo Rollout with both a stable and canary version:

```bash
kubectl argo rollouts set image rollouts-demo rollouts-demo=argoproj/rollouts-demo:yellow
kubectl argo rollouts get rollout rollouts-demo
```

## Argo Traffic Splitting 

Traffic splitting is accomplished in Istio by adjusting traffic weights defined in an Istio VirtualService. When using Argo Rollouts with Istio, a user deploys a VirtualService containing at least one HTTP route containing two HTTP route destinations: a route destination targeting the pods of canary ReplicaSet, and a route destination targeting the pods stable ReplicaSet. Istio provides two approaches for weighted traffic splitting, both approaches are available as options in Argo Rollouts, for example here's Subset-level traffic splitting in Argo: 

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: rollout-example
spec:
  ...
  strategy:
    canary:
      trafficRouting:
        istio:
          virtualService: 
            name: rollout-vsvc        # required
            routes:
            - primary                 # optional if there is a single route in VirtualService, required otherwise
          destinationRule:
            name: rollout-destrule    # required
            canarySubsetName: canary  # required
            stableSubsetName: stable  # required
      steps:
      - setWeight: 5
      - pause:
          duration: 10m
  ```

Remember a single service should be defined, should see something like this:

![image](https://user-images.githubusercontent.com/20936398/147022333-c5b7a077-2075-4601-893a-abe9758b7055.png)
