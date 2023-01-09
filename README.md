# mp3converter-microservice-kubernetes

Key installations
- `MySQL`
- `minikube`
- `docker`
- `kubectl`
- `python3.10`

Key steps:
1. Set up python virtual environment for each service
```shell
python3 -m venv venv
source ./venv/bin/activate
pip3 install -r requirements.txt
```
2. Create each service's driver file and create relevent servers
```python
server = Flask(__name__)
```
and in the end, specify the port each service occupies via
```python
server.run(host="0.0.0.0", port=SERVICE_PORT)
```
3. Setup Gateway service to allow api routing

4. After Gateway service is built, update service host for domain to IP mapping under `etc/host` to include `127.0.0.1 your_app_domain`, and enable 
minikube tunneling for proxy/load balancer between external IPs and server backends, specifically all the NodePorts

5. Create queues within the RabbitMQ admin GUI (`video` and `mp3` in our case), we can keep exchange the same.

Deploying each service into Kubernetes
1. Create Dockerfile Build Docker image under each service's folder
```shell
pip3 freeze > requirements.txt
docker build .
docker tag ${SHA256_IDENTIFIER} ${YOUR_DOCKER_USERNAME}:${APP_VERSION}
docker push ${YOUR_DOCKER_USERNAME}:${APP_VERSION}
```
> Note: here the `--no-cache-dir` param in line 10 is critical for docker build performance (speed) during CI/CD as it avoids installing requirements everytime the image is built
2. Setup configurations in `manifests` folder with all the relevant K8s yaml files, then do
```shell
kubectl detele -f ./manifests
kubectl apply -f ./manifests
```
3. For debugging and changing available pods in K8s, run
```shell
kubectl scale deployment --replicas=${DESIRED_COUNT} ${SPECIFIC_SERVICE}
```
