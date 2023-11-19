# GitLab with Let's Encrypt Using Docker Compose

📙 The complete installation guide is available on my [website](https://www.heyvaldemar.com/install-gitlab-using-docker-compose/).

❗ Change variables in the `.env` to meet your requirements.

💡 Note that the `.env` file should be in the same directory as `gitlab-traefik-letsencrypt-docker-compose.yml`.

Create networks for your services before deploying the configuration using the commands:

`docker network create traefik-network`

`docker network create gitlab-network`

Deploy GitLab using Docker Compose:

`docker compose -f gitlab-traefik-letsencrypt-docker-compose.yml -p gitlab up -d`

Visit the GitLab URL, and sign in with the username `root` and the password from the following command:

`sudo docker exec -it $(sudo docker ps -aqf "name=gitlab-gitlab-1") grep 'Password:' /etc/gitlab/initial_root_password`

Get the GitLab Runner's registration token via this link:

`https://gitlab.heyvaldemar.net/admin/runners`

Note that you need to specify the domain name of the service, previously defined in the `.env` file.

Register the GitLab Runner with an obtained token:

```
REGISTRATION_TOKEN=125DGwcgyrAsVVjUkxTL \
&& docker exec -it $(sudo docker ps -aqf "name=gitlab-runner-1") gitlab-runner register \
--non-interactive \
--url "http://gitlab/" \
--registration-token "$REGISTRATION_TOKEN" \
--executor "docker" \
--docker-image docker:stable \
--description "docker-runner-1" \
--tag-list "docker,linux" \
--run-untagged="true" \
--docker-privileged \
--output-limit "50000000" \
--access-level="not_protected" \
--docker-volumes "/var/run/docker.sock:/var/run/docker.sock"
```

# Запуск Swarm mode

Для запуска в режиме swarm с подтягиванием переменных из .env

```
export $(cat .env) > /dev/null 2>&1; docker stack deploy -c gitlab-traefik-letsencrypt-docker-compose.yml traefik-gitlab
```
