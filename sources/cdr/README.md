# Reportnet CDR

The Central Data Repository is part of the Reportnet architecture. The Central Data Repository is like a bookshelf, with data reports on the environment as submitted to international clients.

This chart is almost configured for production use.

## Network Security Policies

This Helm chart deploys Kubernetes Network Policies to enhance the security of the CDR application. If `defaultNetworkPolicyDeny` is enabled, these policies enforce a **whitelist** approach, meaning that network traffic is only allowed if explicitly permitted.

**Key Network Policy Configurations:**

*   **Instance Pod Egress (`instance.networkPolicy.egress` in `values.yaml`):**  Configures outbound traffic from the `cdr-instance` pods. You can configure egress rules in two ways:

    1. Using `ipBlockRules`: Define IP-based rules with specific ports for each CIDR block:
    ```yaml
    instance:
      networkPolicy:
        egress:
          ipBlockRules:
            - cidr: 10.50.4.0/24  # Legacy service network
              ports:
                - protocol: TCP
                  port: 443     # HTTPS
                - protocol: TCP
                  port: 80      # HTTP
            - cidr: 10.50.5.0/24  # Another network
              ports:
                - protocol: TCP
                  port: 5432    # PostgreSQL
    ```

    2. Using `rawRules`: For more complex scenarios, define individual egress rules using the full power of Kubernetes NetworkPolicy `to` specifications:
    ```yaml
    instance:
      networkPolicy:
        egress:
          rawRules:
            - to:
                - namespaceSelector:
                    matchLabels:
                      kubernetes.io/metadata.name: other-namespace
                  podSelector:
                    matchLabels:
                      app.kubernetes.io/name: service-name
              ports:
                - port: 8080
                  protocol: TCP
    ```

    You can use `rawRules` to define:
    *   **IP Block Rules:** Target egress traffic to specific IP address ranges
    *   **Namespace Selector Rules:** Target pods in other namespaces based on namespace labels and pod labels
    *   **Pod Selector Rules:** Target pods within the same namespace based on pod labels
    *   **Service Account Selector Rules (Advanced):** Target pods based on their service accounts


*   **Varnish Pod Network Policy (`varnish.networkPolicy` in `values.yaml`):** Defines additional ingress and egress rules for the `cdr-varnish` pods.  You can use `varnish.networkPolicy.additionalIngress` and `varnish.networkPolicy.additionalEgress` in `values.yaml` to add custom rules on top of the base policy defined in the templates.

*   **Default Deny Policies:** The chart includes deny Network Policies (`netsecpol-ingress-default.yaml` and `netsecpol-egress-default.yaml`) that, when enabled with `defaultNetworkPolicyDeny.enabled: true`, block all ingress and egress traffic unless overridden by more specific policies. This ensures a secure starting point and encourages explicit definition of allowed traffic.

**Modifying Network Policies:**

To customize the network policies, you should modify the `instance.networkPolicy.egress` and `varnish.networkPolicy` sections within your `values.yaml` file.  Refer to the Kubernetes Network Policy documentation for details on how to define rules using `ipBlock`, `namespaceSelector`, `podSelector`, ports, and protocols.

## Releases

### Version 0.3.44 - 13 March 2025
- Release of dependent chart rn-varnish:0.11.0 [valentinab25 - [`4e7d7ef`](https://github.com/valentinab25/helm-charts/commit/4e7d7efc1bc9b3623be0072821d8fa06cec45694)]

### Version 0.3.43 - 13 March 2025
- Release of dependent chart rn-varnish:0.10.0 [root - [`5fcfe77`](https://github.com/valentinab25/helm-charts/commit/5fcfe77f51f19d00ff8ecf8915be044f72900061)]

### Version 0.3.42 - 05 March 2025
- Release of dependent chart rn-varnish:0.7.0

### Version 0.3.41 - 05 March 2025
- Release of dependent chart rn-varnish:0.2.0

### Version 0.3.40
- Improved network policy configuration by allowing per-CIDR port specifications in ipBlockRules

### Version 0.3.33
- Fixed url in CSP header.

### Version 0.3.32
- Updated ingress's CSP header.

### Version 0.3.31
- Improved instance network security policy template to allow for more flexibility.

### Version 0.3.30
- Added network policies.
- Updated appVersion to 6.7.3-204.
- Updated rn-zeoserver to 0.1.9.
- Updated rn-varnish to 0.1.10.
- Updated rn-local-converters to 0.1.6.
- Updated rn-clamav to 0.1.8.

### Version 0.3.23
- Updated appVersion to 6.7.3-202.

### Version 0.3.22
- Updated appVersion to 6.7.3-201.

### Version 0.3.21
- Updated appVersion to 6.7.3-200.

### Version 0.3.20
- Added missing env variables for auto env cleanuo.

### Version 0.3.19
- Fixed cronjob command for auto fallin, auto env cleanup and auto cleanup.

### Version 0.3.18
- Updated rn-clamav chart to 0.1.5.

### Version 0.3.17
- Updated rn-zeoserver chart to 0.1.6.

### Version 0.3.16
- Fixed cronjob for auto cleanup.

### Version 0.3.15
- Added cronjob for auto cleanup.

### Version 0.3.14
- Updated appVersion to 6.7.3-199.

### Version 0.3.13
- Updated appVersion to 6.7.3-198.

### Version 0.3.12
- Updated appVersion to 6.7.3-197.

### Version 0.3.11
- Updated appVersion to 6.7.3-196.

### Version 0.3.10
- Updated appVersion to 6.7.3-195.

### Version 0.3.9
- Added computed sentry release

### Version 0.3.8
- Added sentrySite and sentryEnvironment variables.

### Version 0.3.7
- Conditionally create and mount zip_cache pvc/pv if zipCacheEnabled is true.

### Version 0.3.6
- Updated rn-clamav and rn-local-converters

### Version 0.3.5
- Converted template values to use single quotes to handle special characters.

### Version 0.3.4
- Removed repository and tag from instance in values.yaml.

### Version 0.3.3
- Added missing env variables for cronautofallin.

### Version 0.3.2
- Fixed values mix-up.

### Version 0.3.1
- Fixed the webforms related questions.

### Version 0.3.0
- Added webforms to the list of services.

### Version 0.2.15
- Updated rn-clamav chart to 0.1.3.
- Updated rn-varnish chart to 0.1.5.

### Version 0.2.14
- Updated appVersion to 6.7.3-194.

### Version 0.2.13
- questions.yaml updates.

### Version 0.2.12
- More updates to the questions.yaml related to ingress.

### Version 0.2.11
- Updated questions.yaml for ingress.

### Version 0.2.10
- Fixed SSRF on Converters/run_conversion.

### Version 0.2.9
- Added nginx server snippet to ingress to prevent SSRF on Converters/run_conversion.

### Version 0.2.8
- Added sentryDSN to questions.yaml.

### Version 0.2.7
- Added zeoAddress to questions.yaml and re-labeled ingress backend service name.

### Version 0.2.6
- Added varnish backend configuration in questions.yaml.

### Version 0.2.5
- Updated rn-zeoserver chart to 0.1.5.

### Version 0.2.4
- Added additional ingress configuration options in questions.yaml.

### Version 0.2.3
- More reorganization of questions.yaml.

### Version 0.2.2
- Reorganized questions.yaml and added more questions.

### Version 0.2.1
- Added questions.yaml.

### Version 0.2.0
- Updated rn-zeoserver chart to 0.1.2.

### Version 0.1.9
- Fixed liveness probe and readiness probe in deployment.

### Version 0.1.8
- Updated readme.
- Moved liveness probes out of the deployment.

### Version 0.1.7
- Refactoring and cleanup.
- Updated rn-varnish chart to 0.1.4.
- Updated rn-local-converters chart to 0.1.2.
- Updated rn-clamav chart to 0.1.2.
- Updated ingress.

### Version 0.1.6
- Refactored to be able to install multiple stacks in the same namespace. Due to the chart naming, we can't solely rely on appl.fullname for service naming.
- Dropped deployment since it's not needed, we can use RELEASE instead.
- Removed storage PVC since it's not needed.

### Version 0.1.5
- Added rabbitmq.create and rabbitmq.name. When create is true, the rabbitmq service will be created.

### Version 0.1.4
- Get the image and tag from image section.

### Version 0.1.3
- Changed rabbitmq to externalName.

### Version 0.1.2
- Updated rn-varnish chart to 0.1.3 and fix ingress path.

### Version 0.1.1
- Updated rn-varnish chart to 0.1.2.

### Version 0.1.0
- Initial release.
