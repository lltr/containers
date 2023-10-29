# Excalidraw

## Excalidraw with collaboration enabled

This container is for the ability to run excalidraw in collaboration mode.

For this to run, the following needs implemented

  1. Deploy [excalidraw-room](https://github.com/excalidraw/excalidraw-room)
  2. Enable tls using a reverse proxy or ingress controller and get a certificate for the collab server backend.
  3. Deploy this container and set the environment variable `COLLAB_ADDR=<Your Collab Server Address>`
  4. Serve the Excalidraw server via TLS behind a reverse proxy as well.
