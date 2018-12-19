The steps to deploy the upstream service running in Nodejs runtime on GKE. 

1. Create a K8S cluster along with istio-beta. Select strict-MTLS for the security.
   `https://cloud.google.com/istio/docs/istio-on-gke/installing`

2. The docker image is already built and uploaded to phanibalaji/istio-sample-images at 
   `https://cloud.docker.com/repository/registry-1.docker.io/phanibalaji/istio-sample-images`
    Otherwise, if you want to build the image by yourself go to step 6 and comeback after completing all the subsequent steps upto step 8.

3. Run the below command to deploy the upstreamNode POD, associated service and detsinationRule.
   ```
   kubectl apply -f upstreamNode.yaml
   ```

4. Install a test POD `sleep` using the below command. The istio download has the sleep.yaml
   ``` 
   kubectl -f apply samples/sleep/sleep.yaml
   ```

5. Test the upstream Node service using the below command
   ```
   kubectl exec -it sleep-79cc87b6b9-4h6zr -- curl http://upstreamnode:8080/upstreamNode  

   Defaulting container name to sleep.
   Use 'kubectl describe pod/sleep-79cc87b6b9-4h6zr -n default' to see all of the containers in this pod.
   {"Message":"Hello from Upstream Nodejs service","version":"1","Time":"2018-12-19T09:47:12.749Z","IP":"10.16.1.9"}
   ```

6. Run the below command to build the image. The Docker file is already provided
   ```
   docker build -t <docker_hub_username>/<your_repo-name> .
   ```

7. Run the container with the below command
   ```
   docker run -p 49160:8080 -d <docker_hub_username>/<your_repo-name>
   curl http://localhost:49160/upstreamNode
   ```
   
8. If test is successful then tag the image and upload to the appropriate your docker hub repository.
   Mention the image location in the `upstreamNode.yaml`
   ```
   image: phanibalaji/istio-sample-images:upstream-node-v1
   ```