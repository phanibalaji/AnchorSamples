# The steps to deploy the downstream service running in GO runtime on GKE. 

This service makes a call to downstream service running in Nodejs. For more information about the upstream service navigate to the `upstreamNode` directory in this repository.

1. First deploy the upstream Nodejs service. For detailed steps see the `readme.md` in `upstreamNode` directory

2. The docker image is already built and uploaded to phanibalaji/istio-sample-images at https://cloud.docker.com/repository/registry-1.docker.io/phanibalaji/istio-sample-images. Otherwise, if you want to build the image by yourself follow the steps under __Steps to build downstream GO image__ comeback after completing all the  steps.

3. Run the below command to deploy the `downstreamgo-v1` POD, associated service and detsinationRule.
```
kubectl apply -f downstreamGo.yaml
```

4. Install a test POD `sleep` using the below command. The istio download has the `sleep.yaml`
```
kubectl -f apply <istio_unzipped_dir>/istio-1.0.6/samples/sleep/sleep.yaml
```

5. Run the following command to list the containers of the pods
```
kubectl get namespace default -o yaml
kubectl get pods -n default
kubectl get pods downstreamgo-v1-d8847485-9g84w -o jsonpath='{.spec.containers[*].name}' && echo ""
kubectl get pods sleep-7ffd5cc988-6ldh2 -o jsonpath='{.spec.containers[*].name}' && echo ""
```

5. Test the downstream Node service using the below command.
```
kubectl exec -it sleep-7ffd5cc988-6ldh2 -- curl http://downstreamgo:8080/downstreamGo && echo ""
Defaulting container name to sleep.
Use 'kubectl describe pod/sleep-7ffd5cc988-6ldh2 -n default' to see all of the containers in this pod.
{"Message":"Hello from Upstream Nodejs service","version":"1","Time":"2018-12-19T11:58:28.375Z","IP":"10.16.0.16"}
```

# Steps to build downstream GO image
1. Run the below command to build the image. The Docker file is already provided
```
docker build -t <docker_hub_username>/<your_repo-name> .
```

2. Run the container with the below command
```
docker run -p 49161:8080 -d <docker_hub_username>/<your_repo-name>
curl http://localhost:49161/downstreamGo
```

3. If test is successful then tag the image and upload to the appropriate your docker hub repository. Mention the image location in the `downstreamGo.yaml`
```
image: phanibalaji/istio-sample-images:downstream-go-v1
```
