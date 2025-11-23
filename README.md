# containers
Docker containers on my synology server

<img width="29" alt="image" src="https://github.com/user-attachments/assets/3523281e-fbf2-49c6-9281-2133c832533a" />

---

## 📁 Folder Structure

```
/volume1/docker/plex/
├── config/          # Plex configuration (library, settings, etc.)
└── media/           # Your movies/TV/music folders (or mount separately)
```

---

## 📄 docker-compose.yml

```yaml
version: '3.3'

services:
  plex:
    image: plexinc/pms-docker:latest
    container_name: plex
    restart: unless-stopped
    network_mode: host  # recommended for DLNA/local discovery
    environment:
      - PUID=1050            # Replace with your plexuser UID
      - PGID=100             # Replace with your plexuser GID
      - TZ=Europe/Berlin     # Change to your timezone
      - PLEX_CLAIM=...       # https://account.plex.tv/en/claim
    volumes:
      - /volume1/docker/plex/config:/config
      - /volume1/media:/media  # Adjust if your media is elsewhere
```

## ⚙️ Synology 

### 1. Create a dedicated Plex user

- Go to **Control Panel > User & Group**
- Create `plexuser`
- Disable DSM login, no admin rights

### 2. Find UID and GID

SSH into your NAS:

```bash
id plexuser
```

Example:

```
uid=1050(plexuser) gid=100(users)
```

Use these values for `PUID` and `PGID` in the `docker-compose.yml`.

---

### 3. Fix Permissions (one-time setup)

```bash
sudo chown -R 1050:100 /volume1/docker/plex/config
sudo chmod -R 775 /volume1/docker/plex/config
```

Optional for media (read-only):

```bash
sudo chown -R 1050:100 /volume1/media
sudo chmod -R 755 /volume1/media
```

---

### 4. Run Container

```bash
cd /volume1/docker/plex
docker-compose up -d
```

---

### 5. Access Plex

Open a browser:

```
http://<your-nas-ip>:32400/web
```

Sign in and start scanning your media library.

---

## ✅ Tips

* Enable hardware transcoding by adding:

  ```yaml
  devices:
    - /dev/dri:/dev/dri
  ```

