# Tanzu for Kubernetes Operators Workshop

This is a workshiop for delivering a focused demo for the operator persona's experience of [VMware Tanzu](https://tanzu.vmware.com/) solutions.

You can currently access a hosted version of the E2E workshop here: [https://via.vmware.com/tanzu-e2e-demo](https://via.vmware.com/tanzu-e2e-demo)

If you already have the Educates operator installed and configured, to deploy and view this sample workshop, run:

```
kubectl apply -f https://raw.githubusercontent.com/tanzu-end-to-end/tko-workshop/main/resources/workshop.yaml
kubectl apply -f https://raw.githubusercontent.com/tanzu-end-to-end/tko-workshop/main/resources/training-portal.yaml
```

This will deploy a training portal hosting just this workshop. To get the URL for accessing the training portal run:

```
kubectl get trainingportal/tko-training-portal
```

The training portal is configured to allow anonymous access. For your own workshop content you should consider removing anonymous access.
