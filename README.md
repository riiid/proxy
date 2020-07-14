# Istio Proxy

***This is a fork of `istio-proxy` that provides `grpc-web` compatibility through downgrading `HTTP/2` to `HTTP/1.1`.***

## Build Instruction

```bash
$ git clone https://github.com/riiid/proxy --single-branch --branch v1.5.1-grpc-web --recursive
$ cd envoy && git apply ../envoy.patch && cd ..
$ docker run -it --detach --name=builder --entrypoint=/bin/bash \
  envoyproxy/envoy-build-ubuntu:f21773ab398a879f976936f72c78c9dd3718ca1e
$ docker cp envoy builder:/envoy
$ docker cp . builder:/proxy
$ docker exec -it builder /bin/bash -c 'cd /proxy && bazel build --config=release //src/envoy:envoy'
$ docker cp builder:/proxy/bazel-bin/src/envoy/envoy ./envoy-custom
$ cat > Dockerfile << EOF
FROM istio/proxyv2:1.5.1
COPY envoy-custom /usr/local/bin/envoy
EOF
$ docker build . -t "istio-proxy:1.5.1-custom" && rm -f Dockerfile
```

---

The Istio Proxy is a microservice proxy that can be used on the client and server side, and forms a microservice mesh. The Proxy supports a large number of features.

Client Side Features:

- *Discovery & Load Balancing*. The Proxy can use several standard service discovery and load balancing APIs to efficiently distribute traffic to services.

- *Credential Injection*. The Proxy can inject client identity, either through connection tunneling or protocol-specific mechanisms such as JWT tokens for HTTP requests.

- *Connection Management*. The Proxy manages connections to services, handling health checking, retry, failover, and flow control.

- *Monitoring & Logging*. The Proxy can report client-side metrics and logs to the Mixer.

Server Side Features:

- *Rate Limiting & Flow Control*. The Proxy can prevent overload of backend systems and provide client-aware rate limiting.

- *Protocol Translation*. The Proxy is a gRPC gateway, providing translation between JSON-REST and gRPC.

- *Authentication & Authorization*. The Proxy supports multiple authentication mechanisms, and can use the client identities to perform authorization checks through the Mixer.

- *Monitoring & Logging*. The Proxy can report server-side metrics and logs to the Mixer.

Please see [istio.io](https://istio.io)
to learn about the overall Istio project and how to get in touch with us. To learn how you can
contribute to any of the Istio components, including the proxy, please 
see the Istio [contribution guidelines](https://github.com/istio/istio/blob/master/CONTRIBUTING.md).
