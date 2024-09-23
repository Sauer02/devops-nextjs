# DevOps Assignment 2

## Created YAML file

In root directory I created only one yaml file `kubernetes.yaml` which contains all 3 steps I implemented.

I already had my image from assigment 1 on my DockerHub `csauercampus/devops-nextjs`.

I am using RollingUpdate to ensure 0-downtime. This gradually replaces old pods with new ones while ensuring a portion of the old pods are still running.

I expose the webapp via the service, by that I can confirm it is up and running by going to localhost:8080.

Also to keep up with the usage I implemented the `HorizontalPodAutoscaler` which introduces more pods when the usage goes above 70% (in my current defintion).

To keep track if my webpage is still served correctly, I also use the Liveness & Readiness probes. I didn't introduce a new endpoint, because for testing purpose, I just point to the about page, if its reachable.</br>
<b>Liveness Probe:</b> Ensures that the container is running. </br>
<b>Readiness Probe:</b> Ensures that the container is ready to serve traffic.

To test the scaling, you can use the tool `k6` were you can run different load tests. Here would be a simple script to run a test (therefore you could set the CPU % limit from 70 to something real small and then with `kubectl get pods -w` watch if there are more replicas created or `kubectl get hpa`)

```Javascript
import http from 'k6/http';
import { sleep } from 'k6';

export let options = {
  stages: [
    { duration: '30s', target: 100 }, // Ramp-up to 100 users over 30 seconds
    { duration: '1m', target: 100 },  // Stay at 100 users for 1 minute
    { duration: '30s', target: 0 },   // Ramp-down back to 0 users
  ],
};

export default function () {
  http.get('http://<your-service-endpoint>/'); // Replace with your app's URL
  sleep(1);
}
```
