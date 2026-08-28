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

    sudo cp etc/vector/vector.yaml /etc/vector/vector.yaml

Start and enable the service.

    sudo systemctl restart vector
    sudo systemctl enable vector
