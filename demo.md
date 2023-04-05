# Use Kubernetes to Deploy the VAT Calculator
This demo deploys the VAT Calculator to a local Kubernetes in Docker (KinD) cluster. It has 4 nodes: 1 control plane node and 3 worker nodes.

To see the nodes:

`
kubectl get nodes
`

## Deploy Version 1 of the VAT Calculator

1. Apply the manifest that includes a service and a deployment of 3 replicas

` 
kubectl apply -f vat-calc.yml
`

2. Confirm the service

`
kubectl get svc vat-svc
`

3. Confirm the pods

`
kubectl get pods -o wide
`

4. In a browser, try to connect to the app:
http://localhost:30001

You will need to enable port-forwarding for this to work on your local device. In a real world scenario, you would deploy this onto a GKE cluster in GCP with a Load Balancer which exposes a real IP address and which balances connections to the running worker nodes where the replicas are running

5. Enable port fowarding:

`
kubectl port-forward service/vat-svc 30001:80
`

6. Refresh the browser to see the VAT calculator

7. Perform a scale-out. 

Edit the manifest and increase the replica count to 5. 

Line 24:

`
  replicas: 5
  `

8. Save the file and apply the manifest:

`
kubectl apply -f vat-calc.yml
`

9. Watch the deployment:

`
kubectl rollout status deployment vat-deploy
`

10. Get the deployment info:

`
kubectl get deploy vat-deploy
`

11. Get the pods:

`
kubectl get pods -o wide
`

12. Scale back to 3 replcas.

Edit the manifest and change replicas to 3. Save and apply the manifest:

`
kubectl apply -f vat-calc.yml
`


## Deploy A Different Version of the VAT Calculator

1. Edit the manifest and change the version from latest to 4

Line 43:

`
    image: victorialloyd/vat-calculator:4
`

2. Apply the manifest:

`
kubectl apply -f vat-calc.yml
`

3. Enable port-forwarding and browse the app. The calculator has a green border.

4. Confirm the deployment revisions:

`
kubectl rollout history deployment vat-deploy
`

5. Look at the replica sets that have been used for the deployments:

`
kubectl get rs
`

6. Get the information for the current replica set. You only need to use a couple of characters from the hash:

`
kubectl describe rs vat-deploy-59
`

Note: the image used for the deployment is version 4

7. Get the info for the earlier deployment:

`
kubectl describe rs vat-deploy-6f
`

Note: the image used for the deployment is version 'latest'

8. Rollback to revision 1 (Grey border).

`
kubectl rollout undo deployment vat-deploy --to-revision=1
`

9. Get the deployment:

`
kubectl get deploy vat-deploy
`

10. Enable port forwarding and confirm the VAT Calculator has a grey border and a tab with the text "Victoria's VAT Calculator"

`
kubectl port-forward service/vat-svc 30001:80
`

CTRL+C to quit port forwarding.

## Clean up

1. Delete the service and deployment:

`
kubectl delete -f vat-calc.yml
`

2. Confirm there is no longer a vat-svc or vat-deploy:

`
kubectl get svc
`

`
kubectl get deploy
`

3. Edit the manifest to point to 'latest' image of the VAT Calculator and save the file. 

Line 43:

`image: victorialloyd/vat-calculator:latest
`
