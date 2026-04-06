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
**Important**: Replace `${FB_USER}` and `${FB_PASSWORD}` in the `environment` section of the `filebrowser-setup` service with the credentials provided by the user.

```yaml
version: "3.8"
services:
  filebrowser-setup:
    image: alpine:latest
    container_name: filebrowser-setup
    restart: "no"
    environment:
      # The agent must insert the user's requested username and password here:
      - FB_USER=${FB_USER}
      - FB_PASSWORD=${FB_PASSWORD}
    command: >
      sh -c "
        if [ ! -f /database/filebrowser.db ]; then
          echo 'Downloading Filebrowser for initial setup...';
          wget -qO- https://github.com/filebrowser/filebrowser/releases/download/v2.30.0/linux-amd64-filebrowser.tar.gz | tar -xz -C /usr/local/bin;
          echo 'Initializing Database...';
          filebrowser config init --database /database/filebrowser.db;
          echo 'Configuring User and Password...';
          filebrowser users add \"$$FB_USER\" \"$$FB_PASSWORD\" --perm.admin --database /database/filebrowser.db;
          echo 'Setup Complete.';
        else
          echo 'Database already exists. Skipping setup.';
        fi
      "
    volumes:
      - filebrowser-database:/database

  filebrowser:
    image: filebrowser/filebrowser:latest
    container_name: filebrowser
    restart: always
    depends_on:
      filebrowser-setup:
        condition: service_completed_successfully
    volumes:
      - filebrowser-database:/database
      # This volume maps the shared files route so new projects can consult uploaded files
      - /data/coolify/shared_files:/srv
    ports:
      - "8080:80"

volumes:
  filebrowser-database:
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
