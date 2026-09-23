# Vector

Install the Vector repo key.

    sudo curl -fsSL https://keys.datadoghq.com/DATADOG_APT_KEY_CURRENT.public -o /etc/apt/keyrings/vector.asc

Create `/etc/apt/sources.list.d/vector.sources` with the following contents.

    Types: deb
    URIs: https://apt.vector.dev
    Suites: stable
    Components: vector-0
    Architectures: amd64
    Signed-By: /etc/apt/keyrings/vector.asc

Install Vector.

    sudo apt update
    sudo apt install vector

Install the configuration.

    sudo mkdir -p /etc/vector/enrichment
    sudo cp config/vector/enrichment/* /etc/vector/enrichment
    sudo mkdir -p /etc/vector/vrl
    sudo cp config/vector/vrl/* /etc/vector/vrl
    sudo cp config/vector/vector.yaml /etc/vector/vector.yaml

Allow the `vector` user to read the Docker socket to collect container logs.

    sudo usermod -a -G docker vector

When the system shuts down, Vector needs to stop before Docker. If Docker stopped first, Vector would hang trying to flush to a missing `otel-collector`. Create `/etc/systemd/system/vector.service.d/shutdown-order.conf` with the following contents.

    [Unit]
    After=docker.service

Start and enable the service.

    sudo systemctl restart vector
    sudo systemctl enable vector
