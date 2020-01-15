
### Image sizes in beginning 
*(without optimizations)*

```
bserv 435 MB  = Backend / ubuntu:16.04
front 531 MB  = Frontend / ubuntu:16.04 
```

### node:alpine based images
*(the smallest configuration)*

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
bserv               latest              01cbf2f9be40        4 minutes ago       211MB
front               latest              1c0021a79b0f        5 minutes ago       307MB
```

### ubuntu:16.04 based images
*(second smallest)*

```
REPOSITORY           TAG                IMAGE ID            CREATED             SIZE
bserv (ubuntu:16.04) latest             976bc9c08443        11 minutes ago      332MB
front (ubuntu:16.04) latest             6b8afbd40317        13 minutes ago      429MB
```

### node:10 based images
*(i.e. bigger than original i.e. even with optimizations)*

```
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
bserv               latest              f78cfae705d9        About a minute ago   969MB
front               latest              c8df439a409d        4 minutes ago        1.07GB

```

*Other images*

```
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
node                10                  d9b29c7fd8c0        20 hours ago         908MB
ubuntu              16.04               c6a43cd4801e        3 weeks ago         123MB
node                alpine              364fb8e7f28a        5 days ago          112MB
nginx               latest              c7460dfcab50        4 days ago          126MB
redis               latest              9b188f5fb1e6        11 days ago         98.2MB
postgres            latest              ec5d6d5f5b34        2 weeks ago         394MB

```
