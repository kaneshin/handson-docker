# handson-docker

Using Cloud Shell on Google Cloud Platform

## Prerequisite

To start an application which is running on container.

### Application

Create golang web application to handle http request.

```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintf(w, "Hello, Container VM!")
	})
	http.ListenAndServe(":8080", nil)
}
```

### Dockerfile

Create docker manifest file.

```
FROM golang:1.6-onbuild
MAINTAINER Shintaro Kaneko <kaneshin0120@gmail.com>
```

### containers.yaml

To run container on GCE.

```
apiVersion: v1
kind: Pod
metadata:
  name: [your-metadata-name]
spec:
  containers:
    - name: [your-container-name]
      image: us.gcr.io/[your-project-id]/[your-tag-name]
      imagePullPolicy: Always
      ports:
        - containerPort: 8080
          hostPort: 80
```

## Run/Push

```shell
# build
docker build -t [your-tag-name] .

# run
docker run -p 8080:8080 [your-tag-name]

# push to container registry
docker tag [your-tag-name] us.gcr.io/[your-project-id]/[your-tag-name]
gcloud docker push us.gcr.io/[your-project-id]/[your-tag-name]
```

## Create instance

```shell
gcloud compute instances create [instance-name] --zone us-central1-b --image-family container-vm --image-project google-containers --machine-type n1-standard-1 --tags "http-server" --metadata-from-file google-container-manifest=containers.yaml
```

## Author

Shintaro Kaneko <kaneshin0120@gmail.com>

