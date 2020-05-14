Deno is the buzzword of the moment, and we still don't know how it will end. 

Honestly, I am very fond of node, but I was intrigued, so I created a hello world with deno, and immediately I tried to containerize it with docker.

Warning!
this example is based on this docker image https://hub.docker.com/r/hayd/deno

Which is not an official deno repository


First of all, we create a file for the deno dependencies, called deps.ts
```typescript
export { serve } from "https://deno.land/std@0.50.0/http/server.ts";
```

Then, we create the main.ts file
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

then add the Dockerfile
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
And then the docker-compose.yml file
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