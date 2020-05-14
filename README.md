Deno is the buzzword of the moment, and we still don't know how it will end. 

Honestly, I am very fond of node, but I was intrigued, so I created a hello world server with deno, and immediately I tried to containerize it with docker.

Warning!
this example is based on this docker image https://hub.docker.com/r/hayd/deno

Which is not an official deno repository

Github Repository: https://github.com/FrancescoXX/deno-docker

Please note the this the first time I use deno, so it is something pretty new also to me

# Short version

you can just clone the repository
```
git clone https://github.com/FrancescoXX/deno-docker
```
navigate to the folder where the docker-compose.yml is located, and run
```
docker-compose up --build
```
Done!
___

# Long version

If you want to follow along, and you use Visual Studio Code, I suggest you install this extension:

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/2wuaa3q9h7t683gymykz.png)

First of all, we create a file for the deno dependencies, called deps.ts.

In Deno, we import third part code directly from urls, no node_modules or package.json is needed

```typescript
export { serve } from "https://deno.land/std@0.50.0/http/server.ts";
```

Then, we create the main.ts file, and import the deps.ts file as a regular ts file

We also define a very simple Server, based on the official example
```typescript
import { serve } from "./deps.ts";

const PORT = 8000;

const s = serve({ port: 8000 });

const body = new TextEncoder().encode("Hello World\n");

console.log(`Server started on port ${PORT}`);
for await (const req of s) {
  req.respond({ body });
}
```

then, we create a very simple Dockerfile, using the hayd/alpine-deno:1.0.0 image
```dockerfile
FROM hayd/alpine-deno:1.0.0

EXPOSE 8000

WORKDIR /app

USER deno

COPY deps.ts .
RUN deno cache deps.ts

COPY . .
RUN deno cache main.ts

CMD ["run", "--allow-net", "main.ts"]
```
And last, we create a very simple docker-compose.yml file, which is no needed for now, but it could be useful in the future, for example, if we start using a database or some other services

We define port 8000 as both inner and outer one and a default network
```yaml
version: "3.7"

services:
  deno:
    image: "deno-docker:0.0.1"
    build: .
    ports:
      - "8000:8000"
    networks: 
      - deno
    
networks:
  deno: {}
```
from the folder where the docker-compose.yml file is located, we build the image:
```
docker-compose build
```

And finally, we can launch the service with this command
```
docker-compose up deno
```

visit localhost:8000 from a browser

Github Repository: Github Repository: https://github.com/FrancescoXX/deno-docker

Done!