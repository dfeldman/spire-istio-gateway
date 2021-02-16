# SPIRE-Istio Gateway

This is a demonstration of making a secure connection from an Istio service mesh to a remote
SPIRE-enabled endpoint.

The demonstration uses two KIND clusters. The setup.sh script sets up both clusters. The two clusters are called "Blue" and "Green".

## Blue cluster
The Blue cluster runs
* Istio, with automatic sidecar injection enabled
* Installed from charts/spire-chart:
  * The SPIRE server
  * The SPIRE agent
  * The k8s workload registrar
* The Envoy proxy, installed from charts/spire-istio-envoy
  * This is configured to run as an Istio service, and accept Istio connections and make outgoing SPIRE-ized connections.
* Ambassador, which can be helpful for debugging
* The "sleep" pod which is just a useful shell to run curl commands

## Green cluster
The Green cluster runs
* The SPIRE agent
  * This is installed from charts/spire-agent
  * It communicates remotely with the SPIRE server on the Blue cluster. It attests via a join token. 
* The quote service, which is a simple service that runs on port 80 that returns a randomly selected quote.
* Another instance of the Envoy proxy, installed from charts/spire-istio-envoy
  * This is configured to run as an externally-facing Kubernetes service (LoadBalancer), and make connections to the Quote service.
 
## To demo
 * Run setup.sh
 * Run cmds/run_test_command.sh to see a quote
 
## TODO
 * setup.sh is not very robust
 * The spire agent is connecting in "insecure bootstrap" mode. This can easily be fixed by following the setup script template in the spire-examples repo, but it adds some addtiional complexity.
 * Envoy is not actually restricting who can access the backend service (authorization). There are Envoy options to enable this but they are not yet configured.
 
## Mac/Windows warning
On Mac and Windows all the scripts should work, but because Docker won't be bound to the external network, it will be hard to check that the Quote service is running or check the status of the Envoy clusters. 

