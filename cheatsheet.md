# Cilium Workshop Cheatsheet


# Step1: deploy app

    kubectl create -f https://raw.githubusercontent.com/techcet/cilium-workshop/master/http-sw-app.yaml

# Step 2: test app traffic

    kubectl exec xwing -ti -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing
    kubectl exec tiefighter -ti -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing


# Step 3: check out cilium
## open a new terminal to have a coinciding session into the cilium pod:
    kubectl exec -ti -n kube-system cilium-xxx bash

## in your cilium terminal window, execute:
    cilium endpoint list
    cilium service list


# Step 4: enforce an L3/L4 policy
    kubectl create -f https://raw.githubusercontent.com/techcet/cilium-workshop/master/l3_l4_policy.yaml
    kubectl get cnp deathstar-cnp -o yaml

## in your cilium terminal window, execute:
    cilium endpoint list
    cilium monitor -t drop

## test the policies in your original terminal window:
    kubectl exec tiefighter -ti -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing
    kubectl exec xwing -ti -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing

## observe the cilium monitor output.

# Step 5: observe more vulnerabilities
    kubectl exec tiefighter -- curl -s -XPUT deathstar.default.svc.cluster.local/v1/exhaust-port


# Step 6: enforce an L7 policy
    kubectl apply -f https://raw.githubusercontent.com/techcet/cilium-workshop/master/l3_l4_l7_policy.yaml
    kubectl get cnp deathstar-cnp -o yaml


## in your cilium terminal window, execute the following to observe l7 policy denies:
    cilium monitor -t l7

    kubectl exec tiefighter -- curl -s -XPUT deathstar.default.svc.cluster.local/v1/exhaust-port
    kubectl exec tiefighter -ti -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing


# Step 7: the hope of the rebels lies within the cilium egress policy
    kubectl create -f https://raw.githubusercontent.com/techcet/cilium-workshop/master/rebel-escape.yaml
    kubectl get cnp
    kubectl get cnp rebels-escape -o yaml
    kubectl get cnp deny-all-egress -o yaml

## ensure the rebels escape but the Empire's tiefighters cannot follow them outside of the cluster
    kubectl exec -ti xwing  -- curl starwars.covalent.link/naboo/landing-request
    kubectl exec -ti tiefighter  -- curl starwars.covalent.link/naboo/landing-request

## in your cilium window, execute:
    cilium endpoint list
    cilium monitor --related-to <endpoint_id_of_xwing>

## rerun the escape:
    kubectl exec -ti xwing  -- curl starwars.covalent.link/naboo/landing-request

## find out how cilium let the rebels escape:
    cilium identity get <identity_of_egress_entity>


# Step 8: troubleshooting tips
## in your cilium window, execute:
    cilium-health get
    cilium-health status
