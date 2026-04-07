# Deploy Filebrowser Workflow

When the user asks to deploy Filebrowser (from `https://github.com/filebrowser/filebrowser`), follow these exact steps:

## 1. Determine Project Context
Before deploying, you must know **which project** the service will be deployed to.
- Use `GET /v1/projects` to fetch the list of existing projects.
- Inform the user of the available projects.
- Ask the user if they want to deploy Filebrowser into one of the existing projects, or if they want to create a new one.
- *Tip for the agent:* Suggest an appropriate project name (e.g., "File Management" or "Infrastructure") based on their request.

## 2. Ask for Credentials and Domain
Once the project is determined, **ask the user** for the following information:
1. The desired **username** for the Filebrowser account.
2. The desired **password** for the Filebrowser account.
3. The **subdomain** or full URL (e.g., `https://files.yourdomain.com`) to be configured for Filebrowser.

*Do not proceed with the deployment until you have collected the project decision and all three configuration items.*

## 3. Deploy via Docker Compose
Use the Coolify API to create a new Service/Application inside the designated environment of the chosen project, using the following `docker-compose.yml`. 
**CRITICAL INSTRUCTION FOR THE AGENT**: You MUST directly hardcode/replace the `el_usuario_aqui` and `la_contrasena_aqui` strings in the YAML text with the actual credentials provided by the user BEFORE sending the payload to the Coolify API. Do not use `${VAR}` format or Coolify will see empty strings.

  # THE AGENT MUST REPLACE THESE STRINGS WITH THE ACTUAL CREDENTIALS BEFORE DEPLOY:
      - FB_USER=${FB_USER}
      - FB_PASSWORD=${FB_PASSWORD}

```yaml
version: '3.8'
services:
  filebrowser:
    image: 'filebrowser/filebrowser:latest'
    container_name: filebrowser
    # We force the container to run as root to solve the "permission denied" error
    user: root
    restart: always
    environment:
     # THE AGENT MUST REPLACE THESE STRINGS WITH THE ACTUAL CREDENTIALS BEFORE DEPLOY:
      - FB_USER=${FB_USER}
      - FB_PASSWORD=${FB_PASSWORD}
      - FB_DATABASE=/database/filebrowser.db
      - FB_ADDRESS=0.0.0.0
      - FB_PORT=80
    expose:
      - '80'
    volumes:
      - '/data/coolify/shared_files:/srv'
      - './database:/database'
    entrypoint: /bin/sh -c
    # Simplified command without comments and using logical operators
    command: >
      "filebrowser config init || true; 
      filebrowser config set --address 0.0.0.0 --port 80 || true;
      filebrowser users add \"$$FB_USER\" \"$$FB_PASSWORD\" --perm.admin || filebrowser users update \"$$FB_USER\" --password \"$$FB_PASSWORD\"; 
      exec filebrowser"


```

## 4. Configure the Subdomain
Once the service is created and you have its `{uuid}`, configure the requested subdomain via the Coolify API:

Make a `PATCH /v1/services/{uuid}` request with the following JSON body (matching the `filebrowser` service name):
```json
{
  "urls": [
    {
      "name": "filebrowser",
      "url": "<user-provided-subdomain>"
    }
  ]
}
```

## 5. Remind the User about the Shared Volume
After the deployment and domain setup are successful, you **MUST remind the user** about the shared volume path. 
Tell them exactly this message:
> "Filebrowser has been successfully deployed. The shared volume where files will be uploaded is located at: `/data/coolify/shared_files`
> 
> When you create new projects or docker composes, remember to configure and mount this exact path (`- /data/coolify/shared_files:/your/path/inside/container`) so they can seamlessly use the uploaded files."
