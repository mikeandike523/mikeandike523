# Installing Docker on WSL2

## Official Resources

 - https://docs.docker.com/engine/install/ubuntu/
 - https://docs.docker.com/engine/install/linux-postinstall/

## Step 1: Remove Conflicting Packages (If Present)

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

## Step 2: Installing Using Docker's Official Apt Repo

For best shell compatibility, place this in its own file (such as `~/add-docker-apt-repo.sh`)and run the file instead of direct paste into shell.

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

## Step 3: Install Latest Docker Release

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Step 4: Test Docker Installation

```bash
sudo docker run hello-world
```

## Step 5: Enable Current User to Run Docker Commands Without Sudo

```bash
sudo groupadd docker
```

```bash
sudo usermod -aG docker $USER
```

```bash
newgrp docker
```

## Step 6: Verify Current User can Run Docker Witout Sudo

```bash
docker run hello-world
```

## Step 7: Installing Docker-Compose

Because we added the Docker apt reposiory in a previous step, it is as easy as:

```bash
sudo apt-get update
sudo apt-get install docker-compose-plugin
```

In the past `docker-compose` was a standalone command. It is still possible to set it up this way, but it is no longer the recommended method by Docker. Instead, `docker-compose` is now a plugin to docker and is called by `docker compose <ARGS>` as opposed to `docker-compose <ARGS>`.

