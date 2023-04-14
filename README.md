# Jax for robot learning: an introduction

| |
| :---: |
| Artificial Mechanical Intelligence @ IIT |
| Diego Ferigo |
| 2023/04/13 |

## Usage with Docker

The notebook is meant to run locally in a docker image.

```bash
git clone https://github.com/diegoferigo/jax_robot_learning_presentation
cd jax_robot_learning_presentation

export DOCKER_UID=$(id -u)
export DOCKER_GID=$(id -g)
export NOTEBOOK_FOLDER=$(pwd)

docker compose --project-directory docker/ up jax_robot_learning         # w/o GPU support
docker compose --project-directory docker/ up jax_robot_learning_nvidia  # w/  GPU support
```

Then click on the URL printed as output.

When you're done running / editing the notebook, press <kbd>Ctrl</kbd>+<kbd>C</kbd> and tear down the service with:

```bash
docker compose --project-directory docker/ down
```

## Usage with online visualizers

If you want to run the notebook in online visualizers like Colab, Binder, etc, you need to install the dependencies first.
Have a look to the specifications of the conda environment exported in [`environment.yml`](./environment.yml).
