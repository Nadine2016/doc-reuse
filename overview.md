---
title: Gloo Platform products
description: Learn about Gloo Platform management components, licensed products, and modules
weight: 10
product: gateway
---

With Gloo Platform, you get a suite of tools to consistently and securely manage your L3-L7 network application traffic. Gloo consists of an installable set of platform management tools that you install in a Kubernetes-based cluster via the Gloo CLI (`meshctl`) or Helm. Then, you unlock various network management capabilities with product or module licenses, as shown in the following figure.

## Shared platform management

When you install Gloo in your cluster, you get several components to provide custom resources, observability, and management capabilities for the product licenses that you have. These components run in your cluster even if you do not add a product license, in which case the components do not report back any data until you start using a product.

The following table describes the components and which products use the components.

| Component | Products that use component | Description |
| --------- | --------------------------- | ----------- |
| Gloo management server |  Gateway, Mesh, Network | After installing one of the Gloo products in your management cluster, the Gloo management server `gloo-mesh-mgmt-server` is deployed and exposed by the `gloo-mesh-mgmt-server` service on port `9900/TCP`. The server is responsible for configuring the Gloo agents in your workload cluster and maintaining the desired state of your environment. When you create Gloo custom resources, the server translates these to the appropriate open source custom resources that your Gloo product is based on, such as Istio, Envoy, or Cilium. Then, the server pushes config changes to the agents to apply in the workload clusters. </br></br> For your workload clusters to communicate with the management server, the `gloo-mesh-mgmt-server` service is automatically set up as a service of type `LoadBalancer`. For more information about the Gloo relay architecture, see [Gloo Platform architecture](/concepts/platform/relay/). |
| Gloo agent | Gateway, Mesh, Network | After registering a workload cluster, the Gloo relay agent is deployed as `gloo-mesh-agent` to each workload cluster and exposed by the `gloo-mesh-agent` service on port `9977`. The agents send product-specific metrics and snapshots of the resources from each workload cluster to the management server. For more information about the Gloo relay architecture, see [Gloo Platform architecture](/concepts/platform/relay/). |
| Prometheus | Gateway, Mesh, Network | The Gloo architecture includes a built-in Prometheus server that scrapes metrics from the Gloo management server. Depending on the Gloo Platform product that you use, product-specific metrics are collected from the apps in your workload clusters and sent to the Gloo agent via gRPC push procedures. The agent then forwards the metrics to the Gloo management server where they are scraped by the Prometheus server. To access the metrics, you can use PromQL queries in the Prometheus UI, or view the metrics graph in the Gloo UI. </br> </br> For more information about the metrics architecture, available metrics, and how to use the built-in Prometheus server, see [Gateway metrics architecture](/concepts/observability/metrics/). For production-level setups, such as using your own Prometheus instance, see [Best practices for collecting metrics in production](/concepts/observability/metrics/#prod).  |
| Gloo UI | Gateway, Mesh, Network | With the UI, you can review the health and configuration of Gloo custom resources, including registered clusters, workspaces, networking, policies, and more. You can even set up external authentication that is synchronized with Kubernetes role-based access control to manage how your users access the UI. For more information, see [Gloo UI](/observability/dashboard/).  |
| Redis | Gateway, Mesh, Network | Redis instances are used to store state data for several Gloo components. You can optionally bring your own Redis instance. <ul><li>Management server: Gloo stores the state of the custom resources in each registered cluster in Redis. If you see state reconciliation errors, you can try [restarting Redis](/troubleshooting/gloo/redis).</li><li>Dashboard: The Gloo UI (dashboard) uses the data in Redis to display resources in the UI.</li><li>External auth (Gateway, Mesh): The external auth server stores its configuration data in a Redis instance that is separate from the one that the management server and dashboard use.</li><li>Rate limiting (Gateway, Mesh): The rate limiting server stores its configuration data in a Redis instance that is separate from the one that the management server and dashboard use.</li></ul> |

## Licensed products

Product licenses unlock certain capabilties in Gloo Platform. For example, with a Gloo Mesh license, your Gloo Platform agent installs Mesh custom resource definitions (CRDs) in each registered cluster. With these CRDs, you can consistently manage your application networking resources across clusters.

| Product | Related open source projects | Description |
| ------- | ---------------------------- | ----------- |
| [Gateway](https://docs.solo.io/gloo-gateway/) | Envoy, Istio | Gloo Gateway is an API gateway based on Envoy and Istio open source technologies. A gateway license unlocks custom resources such as virtual gateways, route tables, and policies so that you can control network traffic into (ingress) and out from (egress) your clusters. You get traffic manipulation features, such as Envoy filters for resilience and transformation. You can also secure ingress traffic with security filters such as web application firewall (WAF), data loss prevention (DLP), external auth, and rate limiting.  |
| [Mesh](https://docs.solo.io/gloo-mesh-enterprise/) | Istio | Gloo Mesh manages Istio-based service meshes across clusters and infrastructure providers, and secures communication between workloads via mTLS. A mesh license unlocks hardened, FIPS-compliant Istio images with `n-4` version support. You get a simplified management experience for multi-tenancy, service isolation, federation, and east-west traffic management. Gloo Mesh even automatically discovers your Istio resources and translates them into the appropriate Gloo custom resources so that intelligent, multicluster failover works out of the box.|
| [Network](https://docs.solo.io/gloo-network/) | eBPF, Cilium | Gloo Network is a Cilium-based container network interface (CNI) plug-in that leverages the Linux kernel technology eBPF to provide connectivity, security, and observability for containerized workloads. You can use Gloo policies to consistently apply L3 and L4 access control across all the services in your multicluster environment. If you use Network with Mesh or Gateway, you can even reuse the same access policies to add L7 access control.|

## Licensed modules

Modules further extend select products with licensed capabilities. The license that you use when installing or upgrading Gloo Platform can include both a product and module, instead of separate licenses. For example, you might have a Gloo Gateway with GraphQL license to use the Gateway product along with the GraphQL module.

| Module | Products the module can extend | Description |
| ------ | ------------------------------ | ----------- | 
| [GraphQL](/graphql/) | Gateway, Mesh | GraphQL is a server-side query language and runtime you can use to expose your APIs as an alternative to REST APIs. GraphQL allows you to request only the data you want and handle any subsequent requests on the server side, saving numerous expensive origin-to-client requests by instead handling requests in your internal network. By building GraphQL capabilties into the Gloo ingress or east-west gateways, Gloo extends GraphQL with route-level networking logic. For example, the gateway might rate limit, authorize, and authenticate requests.  |
