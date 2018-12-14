# Ansible Role: (OSM) Tile proxy

Expectation: no other sites on host, no ssl termination on host (usage inside vm/container, proxied from outside)


```yaml
- hosts: staticmap
  roles:
    - role: ../../roles/pludoni.osm-tiles-proxy
      nginx_tiles_cache_hostname: 'tiles.pludoni.de'
```


available variables (default),

- defines a cache proxy under tiles.example.com/osm/{z}/{x}/{y}/png
- ...which proxies all requests to {a,b,c}.tile.openstreetmap.org
- ...and saves that to /var/tiles_cache/osm, maximum 20GB, valid for 14 days to cache by browser

```yaml
nginx_tiles_cache_hostname: 'tiles.example.com'
nginx_tiles_cache_manage_nginx: true
nginx_tiles_cache_site_name: 'default'

nginx_tiles_cache_locations:
  - name: openstreetmap
    max_size: 20g
    path: "/var/tiles_cache/osm"

nginx_tiles_cache_upstreams:
  - name: openstreetmap_backend
    servers:
    - a.tile.openstreetmap.org
    - b.tile.openstreetmap.org
    - c.tile.openstreetmap.org

nginx_tiles_cache_instances:
  - name: osm_mapnik
    location: "/osm/"
    # must end on / to pass correct path to osm:
    dest_url: "http://openstreetmap_backend/"
    cache_location_name: openstreetmap
    valid_time: 14d
```
