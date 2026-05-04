# Pre-workshop installation

> **Only install. Don't run anything yet.** We'll start the stack together at the beginning of the workshop.

If you're walking into the room early, this is a great use of those 10 minutes. None of these steps are hard, but they add up — and Wi-Fi at conferences is rarely your friend.

## Prerequisites

We assume **Docker** and **`docker compose` v2** are already installed and working on your machine. Confirm with:

```bash
docker --version
docker compose version
```

Both must print a version. `docker compose` (with a space) is v2 — `docker-compose` (with a hyphen) is the deprecated v1 and will not work for this workshop.

If either command fails, install Docker Desktop (<https://www.docker.com/products/docker-desktop/>) or follow your Linux distro's instructions (<https://docs.docker.com/engine/install/>) before the workshop.

## What you'll install

| Tool | What it does | Required |
| --- | --- | --- |
| syft | Generates SBOMs | Yes |
| grype | Scans for vulnerabilities (EPSS + risk) | Yes |
| trivy | Alternative scanner (vulns + IaC + secrets) | Yes |
| oras | Pushes / pulls OCI artifacts | Bonus lab 7 |

You'll also **pre-download** the Dependency Track `docker-compose.yml` and **pre-pull** the `nginx:1.24-alpine` image.

---

## 1. syft — SBOM generator

### macOS

```bash
brew install syft
```

### Linux / curl

```bash
curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh \
  | sh -s -- -b /usr/local/bin
```

### Windows

```powershell
winget install Anchore.Syft
```

### Verify

```bash
syft version
```

Docs: <https://oss.anchore.com/docs/installation/syft/>

---

## 2. grype — vulnerability scanner

### macOS

```bash
brew install grype
```

### Linux / curl

```bash
curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh \
  | sh -s -- -b /usr/local/bin
```

### Windows

```powershell
winget install Anchore.Grype
```

### Verify

```bash
grype version
```

Docs: <https://github.com/anchore/grype>

---

## 3. trivy — broader scanner

### macOS

```bash
brew install trivy
```

### Debian / Ubuntu

```bash
sudo apt-get install wget gnupg
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key \
  | sudo gpg --dearmor -o /usr/share/keyrings/trivy.gpg
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" \
  | sudo tee /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

### RHEL / Fedora

```bash
sudo dnf install -y https://github.com/aquasecurity/trivy/releases/latest/download/trivy_$(uname -m).rpm
```

### Windows

```powershell
winget install AquaSecurity.Trivy
```

### Verify

```bash
trivy --version
```

Docs: <https://trivy.dev/docs/latest/getting-started/>

---

## 4. oras — OCI artifact CLI (bonus lab)

Only needed for Lab 7. If you're tight on time at the venue, you can skip this and install during the morning.

### macOS

```bash
brew install oras
```

### Linux

```bash
VERSION="1.2.0"
curl -LO "https://github.com/oras-project/oras/releases/download/v${VERSION}/oras_${VERSION}_linux_amd64.tar.gz"
mkdir -p oras-install/
tar -zxf oras_${VERSION}_*.tar.gz -C oras-install/
sudo mv oras-install/oras /usr/local/bin/
rm -rf oras_${VERSION}_*.tar.gz oras-install/
```

### Windows

```powershell
winget install ORAS.oras
```

### Verify

```bash
oras version
```

Repo: <https://github.com/oras-project/oras>

---

## 5. Pre-download Dependency Track

Get the compose file ready — but **do not start it**.

```bash
mkdir -p dtrack-workshop && cd dtrack-workshop
curl -LO https://dependencytrack.org/docker-compose.yml
```

You'll end up with a `docker-compose.yml` in `dtrack-workshop/`. Leave it. We'll start the stack together.

---

## 6. Pre-pull the workshop image

Lab 2 uses `nginx:1.24-alpine`. Pull it now so you're not racing the venue Wi-Fi:

```bash
docker pull nginx:1.24-alpine
```

---

## Final check

Run all of these — they should all print versions cleanly:

```bash
syft version && \
grype version && \
trivy --version && \
oras version
```

Plus the compose file and image:

```bash
ls dtrack-workshop/docker-compose.yml
docker image inspect nginx:1.24-alpine > /dev/null && echo "nginx image present"
```

If anything fails, find the presenter when you arrive — easier to fix in person than over a flaky network.

---

## A few reminders

- **Don't `docker compose up` yet.** We start it together.
- Dependency Track binds to port **8080**. If something else on your machine uses that port, plan to either free it or edit the `docker-compose.yml` to map a different host port (e.g. `8081:8080`).
- The vulnerability database download on first start can take several minutes. That's normal.
