# List networks
docker network ls

# Create network
docker network create my-network-demo

# Inspect network
docker network inspect my-network-demo

# Create container with network
docker run -dit --name Containers1 --network my-network-demo ubuntu

# Connect existing container
docker network connect my-network-demo Containers2

# Disconnect container
docker network disconnect my-network-demo Containers2
