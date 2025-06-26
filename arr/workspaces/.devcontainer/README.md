docker build -t platfotm:latest -f .devcontainer/Dockerfile .


docker buildx build --platform linux/amd64 -t platfotm:latest -f .devcontainer/Dockerfile .
