# Pre-workshop installation

## Assumed already installed

- **Docker** + **`docker compose / docker-compose` v2**: confirm with `docker --version` and `docker compose version`
- **curl**: for downloads and the API lab

## What you'll install

| Tool | What it does |
| --- | --- |
| syft | Generates SBOMs |
| grype | Scans for vulnerabilities (EPSS + risk) |
| oras | Pushes / pulls OCI artifacts |

Plus: bring up Dependency Track once (so images + vuln DB are cached) and pre-pull the `nginx:1.24-alpine` image.

---

## 1. syft: SBOM generator

```bash
# macOS
brew install syft

# Linux
curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin

# Windows
winget install Anchore.Syft
```

Verify: `syft version`

Docs: <https://oss.anchore.com/docs/installation/syft/>

---

## 2. grype: vulnerability scanner

```bash
# macOS
brew install grype

# Linux
curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh | sh -s -- -b /usr/local/bin

# Windows
winget install Anchore.Grype
```

Verify: `grype version`

Docs: <https://oss.anchore.com/docs/installation/grype/>

---

## 3. oras: OCI artifact CLI

```bash
# macOS
brew install oras

# Windows
winget install ORAS.oras
```

For Linux, see the install docs (tarball download).

Verify: `oras version`

Docs: <https://oras.land/docs/installation>

---

## 4. Dependency Track: download & start

Get the compose file and bring the stack up. This pulls the apiserver / frontend / postgres images and downloads the vulnerability database (several minutes on first start).

```bash
mkdir -p dtrack-workshop && cd dtrack-workshop
curl -LO https://dependencytrack.org/docker-compose.yml
docker compose up -d
```

Wait until <http://localhost:8080> loads and the vuln DB has finished downloading (Administration → Notifications, or just watch the apiserver logs settle). You can stop it again afterwards with `docker compose down`, the volumes keep the cached data.

Docs: <https://docs.dependencytrack.org/getting-started/deploy-docker/>

---

## 5. Pre-pull the workshop image

```bash
docker pull nginx:1.24-alpine
```

---

## Final check

```bash
syft version && grype version && oras version
docker compose -f dtrack-workshop/docker-compose.yml ps
docker image inspect nginx:1.24-alpine > /dev/null && echo "nginx image present"
```

If anything fails, find the presenter when you arrive, easier to fix in person than over flaky Wi-Fi.

---

## Reminders

- Dependency Track binds to port **8080**. Free that port or remap in the compose file (e.g. `8081:8080`).
- The vulnerability database download on first start takes several minutes. That's normal: let it finish before stopping the stack.
