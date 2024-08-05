# buildbox-casd - for bringing up a BuildBox-CASD REAPI cache server

## Default Configuration

A [default configuration](defaults/main.yml) is provided in this role. It establishes a cache service based on the freedesktop-sdk buildbox-casd Docker image with persistent storage. The following variables are defined:

- `casd_cache`: the path to the cache on the host
- `casd_image`: the Docker image to use in instantiating the container
- `casd_container`: the container name
- `casd_network`: the name of the Docker network to create and attach the service container to
- `casd_network_connected_containers`: a list of containers to connect to the network being created for buildbox-casd
- `casd_port`: the port the service listens to in the container and which is exposed on the container
- `casd_published_ports`: a list of docker port specifications to be published. This does not affect whether the port on which buildbox-casd is listening is exposed.
- `casd_bind_address`: the address the service listens to inside the container
- `casd_quota_high`: the maximum local cache size
- `casd_cache_mnt`: the path to bindmount the cache to in the container
- `casd_cmd`: the entrypoint of the container
- `casd_bind_path`: path to a UNIX socket to serve on.  If set, `casd_bind_address` and `casd_port` are ignored entirely and the service is not exposed.
- `casd_extra_mounts`: additional docker volume mounts to add to the container.  Useful for sharing a UNIX socket
- `casd_proxy_certdir`: the path to copy cert/key data to on the host, this step is skipped if the path is left empty
- `casd_proxy_{cas,ac,asset,execution}_url`: URL to a remote service to proxy requests to
- `casd_proxy_{cas,ac,asset,execution}_server_cert`: CA certificate to check the remote server against
- `casd_proxy_{cas,ac,asset,execution}_client_{cert,key}`: TLS keypair used for client authentication against the remote server
- `casd_metrics_mode`: option passed to `--metrics-mode` of buildbox-casd
- `casd_metrics_publish_interval_secs`: how often metrics get published by buildbox-casd, in seconds
- `casd_labels`: a dictionary containing a set of labels applied to the container

## Example Configurations

### Standalone Cache Server

Here the default should work just fine.

### Caching Proxy

Acts as a caching proxy for requests to another REAPI cache.
Let's suppose we wish to act for a CAS and Action Cache operating at `https://re.example.com` and a Remote Asset server at `https://ra.example.com`.
Then, our configuration would look somewhat like:

```yaml
casd_proxy_cas_url: "https://re.example.com"
casd_proxy_ac_url: "https://re.example.com"
casd_proxy_asset_url: "https://ra.example.com"
```

If the endpoints require different TLS certificates, then this can be handled by the appropriate variables.

### Serving from a UNIX socket

It is also possible to have `buildbox-casd` serving only on a UNIX socket.
In this case, we need to set the `casd_bind_path` variable, which overrides the effects of `casd_bind_address` and `casd_port`.

```yaml
casd_extra_mounts:
  - "casd_sock:/sock"
casd_bind_path: "/sock/casd.sock"
```
