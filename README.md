# qbittorent-plex-helm-chart

A helm chart to deploy qbittorrent and plex.

/!\ This helm chart assumes that you have configured traefik[1] in your cluster.

[1] https://traefik.io/

## Why deploy this in a kubernetes cluster ?

Why not? Plex is more for running on a server in a living room, but it works great in kubernetes too. The main limitation is storage, it's not possible to store all the downloaded media (or it will cost you a lot of money). My usage is simply to download, watch, then delete once the movie is watched. In this case, deploying to here works well (even if it's totally over-engineered, but hey, it's cool kubernetes).
You should also pay attention to the requested cpu value if you want to transcode your media.

## Why qbittorent and plex are in the same pod ? 

In order to plex to access to media downloaded by qbittorent we need to share a pv. However, despite the fact that kubernetes implement multiple access to a pv[1], cloud provider ovh does not provide multiple mount of a pv to multiple node[2].

[1] https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[2] https://docs.ovh.com/au/en/kubernetes/setting-up-a-persistent-volume/#access-modes

## Register plex server

There is two ways of doing it :
- Use a claim (/!\ A claim is only valid during 4 minutes)[1]
- Go to your plex host and login[2]

If you use the second option, the configuration of the server claim token in the table is not necessary.

[1] https://www.plex.tv/claim/
[2] https://support.plex.tv/articles/200264746-quick-start-step-by-step-guides/

## Value

### Qbittorent

| Value                                | Definition                                      | Default                      |
|--------------------------------------|-------------------------------------------------|------------------------------|
| qbittorent.secrets.PLEX_CLAIM        | Qbittorent server claim                         | ""                           |
| qbittorent.image                     | Qbittorent server docker image                  | linuxserver/qbittorent:4.5.1 |
| qbittorent.config_folder.size        | Size of the qbittorent config folder            | 3Gi                          |
| qbittorent.downloads_folder.size     | Size of the qbittorent download folder          | 3Gi                          |
| qbittorent.host                      | Host of your qbittorent server                  | ~                            |
| qbittorent.resources.requests.memory | Qbittorent container memory request             | 100Mi                        |
| qbittorent.resources.requests.cpu    | Qbittorent container cpu request                | 50m                          |
| qbittorent.resources.limits.memory   | Qbittorent container memory limit               | 150Mi                        |
| qbittorent.resources.limits.cpu      | Qbittorent container cpu limit                  | 250m                         |
| qbittorent.secrets                   | Variables to be injected into the container env | {}                           |
| qbittorent.mmiddlewares              | List of traefik middlewares to use              | []                           |


### Plex

| Value                          | Definition                                      | Default                 |
|--------------------------------|-------------------------------------------------|-------------------------|
| plex.image                     | Plex server docker image                        | linuxserver/plex:1.30.2 |
| plex.config_folder.size        | Size of the plex config folder                  | 10Gi                    |
| plex.host                      | Host of your plex server                        | ~                       |
| plex.resources.requests.memory | Plex container memory request                   | 150Mi                   |
| plex.resources.requests.cpu    | Plex container cpu request                      | 50m                     |
| plex.resources.limits.memory   | Plex container memory limit                     | 250Mi                   |
| plex.resources.limits.cpu      | Plex container cpu limit                        | 250m                    |
| plex.secrets                   | Variables to be injected into the container env | {}                      |
| plex.secrets.PLEX_CLAIM        | Plex server claim                               | ""                      |
| plex.mmiddlewares              | List of traefik middlewares to use              | []                      |


### Hosting

This chart will soon be hosted in a public repository (curently working on it).