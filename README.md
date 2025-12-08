
# Linkfish Utilities (Python)
**Including DBT and Python Stack using UV**

The idea is to have one central reference for all our Python dependencies, DBT being one of them.
UV makes this possible because it allows us to manage Python packages via a central lock file (`uv.lock`), which can be updated conveniently, as new versions of dependencies are released.

## Using the Docker Container

The container provides a full-fledged Python and DBT environment.

Usually you will need to mount data and project folders into the container:

```yaml
# docker-compose.yml
services:
    lf-dbt:
        # ... other settings ...
        volumes:
            - /coasti/data/:/coasti/data/
            - /coasti/products/haushaltplus/:/coasti/products/haushaltplus/
```

To build the container and run commands:

```bash
# build the container, only needed once.
docker compose build

# to run dbt commands, e.g. check version
docker run -it --rm lf-dbt dbt --version
```

Often you will need some setup, like loading environment variables.
Then, it can be helpful to define an init command that can be reused easily:

```bash
INIT_CMD="cd /coasti/products/haushaltplus; set -a; source ./config/.env; set +a;"
docker run -it --rm lf-dbt bash -c "$INIT_CMD dbt version"
```

## Updating Dependencies in this Repo

- Clone to your Device

- [Get UV](https://docs.astral.sh/uv/getting-started/installation/)

- Check `pyproject.toml` if you want to update min/max versions

- Check what package updates are available
```bash
uv lock --dry-run --upgrade
# or
uv tree --outdated
```

- Update dependencies and the `uv.lock`
```bash
uv sync --upgrade --no-install-project
```

- Put the changes into `requirements.txt`
```bash
uv pip compile pyproject.toml -o requirements.txt
```

- Update changelog, and possibly version number in pyproject.toml

- Open PR


## Useful UV Commands

```bash
# Find out which python exe uv will use (could be system, conda, venv, ...)
uv python find
```

## Furhter Reading

- [UV Cheat Sheet](https://ricketyrick.github.io/uv_cheat_sheet/uv-cheat-sheet.html)
- [Using UV and Conda together](https://medium.com/@datagumshoe/using-uv-and-conda-together-effectively-a-fast-flexible-workflow-d046aff622f0)
    TLDR: Use Conda for heavy, non-native (cpp) dependencies like pytorch.
- [UV Discovery of Python Envs (including Conda)](https://docs.astral.sh/uv/pip/environments/#discovery-of-python-environments)
