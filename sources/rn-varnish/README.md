# Reportnet Varnish

Varnish for Reportek.

[See more](https://github.com/eea/eea.docker.varnish-reportek)

## Configuration

- `timezone` - Time zone.
- `varnishHTTPPort` - Port for HTTP traffic.
- `varnishHTTPSPort` - Port for HTTPS traffic.
- `varnishSize` - Size of the varnish cache.
- `autoKillCron` - Auto kill cron.
- `varnishCFGContent` - Content of the configuration file.
- `varnishBackend` - Backend to use.
- `varnishBackendPort` - Port of the backend.
- `varnishDNSTTL` - TTL for DNS.
- `varnishBERESPTTL` - TTL for BERESP.
- `varnishBERESPGrace` - Grace period for BERESP.
- `varnishBERESPKeep` - Keep period for BERESP.
- `networkPolicy.enabled` - Enable network policy. Defaults to false.
- `networkPolicy.additionalIngress` - Additional ingress rules to be added to the default ones. Defaults to [].
- `networkPolicy.additionalEgress` - Additional egress rules to be added to the default ones. Defaults to [].
- `networkPolicy.spec` - Additional network policy specifications to be merged with the policy. **Note**: Defining `ingress` or `egress` in spec will completely override the default rules and `additional*` rules. Defaults to {}.
- `backendSelectorLabels` - Labels to select the backend pods.

Default network policy includes:
- Ingress:
  - Allow access from ingress-nginx in kube-system namespace
  - Allow access from same namespace
- Egress:
  - Allow DNS resolution (kube-dns/coredns in kube-system namespace)
  - Allow access to the backend pods. These need to be defined in the backendSelectorLabels.

Example using additionalIngress/Egress (adds to defaults):
```yaml
networkPolicy:
  additionalIngress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
  additionalEgress:
    - to:
        - podSelector:
            matchLabels:
              app: backend
```

Example using spec (overrides defaults):
```yaml
networkPolicy:
  spec:
    ingress:
      - from:
          - podSelector:
              matchLabels:
                app: custom-frontend
    egress:
      - to:
          - podSelector:
              matchLabels:
                app: custom-backend
```

Example in parent chart's `values.yaml`:

Assuming your parent chart defines a backend service whose pods have the labels `app: my-backend-app` and `component: backend`, you would configure your parent chart's `values.yaml` like this:

```yaml
rn-varnish: # Assuming you named your rn-varnish subchart "rn-varnish"
  networkPolicy:
    enabled: true
    backendSelectorLabels:
      app: my-backend-app
      component: backend
  varnishBackend: my-backend-service # Name of your backend service in the parent chart
  varnishBackendPort: 8080
```

## Releases

### Version 0.9.0 - 13 March 2025
- Automated release of eeacms/reportek-varnish:10.0-1.1 [root - [`4c693bd`](https://github.com/valentinab25/helm-charts/commit/4c693bd7186baca0314ce0c9ae0a47d9e8d89c56)]

### Version 0.8.0 - 13 March 2025
- Automated release of eeacms/reportek-varnish:10.0-1.0

### Version 0.7.1 - 13 March 2025
- upgrade varnish test [valentinab25 - [`3ec9a0b`](https://github.com/valentinab25/helm-charts/commit/3ec9a0b5f1fe7aca163ef69cfed60cbb42580e68)]

### Version 0.7.0 - 05 March 2025
- Automated release of eeacms/reportek-varnish:9.0-1.2

### Version 0.6.0 - 05 March 2025
- Automated release of eeacms/reportek-varnish:9.0-1.1

### Version 0.5.0 - 05 March 2025
- Automated release of eeacms/reportek-varnish:9.0-1.0

### Version 0.4.0 - 05 March 2025
- Automated release of eeacms/reportek-varnish:8.0-1.0

### Version 0.3.0 - 05 March 2025
- Automated release of eeacms/reportek-varnish:7.7-1.0

### Version 0.2.0 - 25 February 2025
- Automated release of eeacms/reportek-varnish:7.6-1.0

### Version 0.1.10
- Fixed nintend for backendSelectorLabels in networkpolicy template.

### Version 0.1.9
- Set networkPolicy enabled to false by default.
- Added backendSelectorLabels to select the backend pods.

### Version 0.1.8
- Renamed network policy metadata component label to network-policy.

### Version 0.1.7
- Added additional ingress/egress rules support

### Version 0.1.6
- Added NetworkPolicy support

### Version 0.1.5
- Updated appVersion to 7.5-1.0

### Version 0.1.4
- Some refactoring and added component labels

### Version 0.1.3
- Removed autoKillCron variable since, using it, causes varnish to no longer respond

### Version 0.1.2
- Changed default port to 8080

### Version 0.1.1
- Added enabled flag

### Version 0.1.0
- Initial release
