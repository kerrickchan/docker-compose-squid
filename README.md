# Squid Proxy Docker Setup

This repository contains a simple Docker Compose setup for running a [Squid](http://www.squid-cache.org/) caching proxy using the official `ubuntu/squid` image.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Usage

1. **Start the proxy:**
   Run the following command in the root of this directory to start the Squid proxy in the background:
   ```bash
   docker compose up -d
   ```

2. **Stop the proxy:**
   To stop and remove the containers, run:
   ```bash
   docker compose down
   ```

3. **View logs:**
   To view the logs of the running proxy:
   ```bash
   docker compose logs -f
   ```

## Configuration

- **Port:** The proxy listens on port `3128` by default.
- **Config File:** The main configuration is managed via the `squid.conf` file in this directory. It is mounted directly into the container at `/etc/squid/squid.conf`. Edit this file to customize your access control lists (ACLs), caching rules, and other settings. You will need to restart the container (`docker compose restart squid`) for changes to take effect.

## Data Persistence (Optional)

By default, the cache and log data are ephemeral and will be lost if the container is removed. If you want to persist this data, you can uncomment the volume mappings in the `docker-compose.yml` file:

```yaml
    volumes:
      - ./squid.conf:/etc/squid/squid.conf
      - ./cache:/var/spool/squid # Persist cache data
      - ./logs:/var/log/squid    # Persist log data
```

*Note: If you enable these volumes, ensure the local `./cache` and `./logs` directories exist and have the appropriate permissions for the Squid process inside the container to write to them.*

## macOS Wi-Fi Proxy Setup

To route your Mac's Wi-Fi traffic through this proxy:

### Via System Settings

1. Open **System Settings** → **Wi-Fi** → click **Details…** on your connected network
2. Select **Proxies** in the sidebar
3. Enable **Web Proxy (HTTP)** and set:
   - Server: `127.0.0.1`
   - Port: `3128`
4. Enable **Secure Web Proxy (HTTPS)** and set:
   - Server: `127.0.0.1`
   - Port: `3128`
5. Click **OK**, then **Apply**

### Via Terminal

```bash
# Enable HTTP proxy
networksetup -setwebproxy Wi-Fi 127.0.0.1 3128

# Enable HTTPS proxy
networksetup -setsecurewebproxy Wi-Fi 127.0.0.1 3128
```

### Disable Proxy

```bash
networksetup -setwebproxystate Wi-Fi off
networksetup -setsecurewebproxystate Wi-Fi off
```

> **Note:** If accessing the proxy from another device on your network, replace `127.0.0.1` with your Mac's local IP address (find it in **System Settings → Wi-Fi → Details → TCP/IP**).
