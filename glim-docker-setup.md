Using GLIM usingDocker with GPU
### 1 Clone the Repository:
This command clones the GLIM repository from GitHub to your local machine, downloading its contents to the specified directory.
```
git clone https://github.com/koide3/glim.git /path_where_glim_will_download
```
### 2 Pull the Docker Image:
This command downloads the Docker image with CUDA support from Docker Hub. The image includes the necessary software and drivers for GPU support.
```
docker pull koide3/glim_ros2:humble_cuda12.2
```
### 3 Verify the Docker Image:

Use this command to check if the Docker image has been downloaded correctly and is available on your local machine.
```
sudo docker images
```
Make sure you have X11 forwarding enabled on your host machine. You can do this by running:
```
xhost +local:docker
```
### 4 Prepare the config Folder:
Ensure the config folder is in your current working directory. This folder contains configuration files needed by the Docker container. You can either copy it from the downloaded repository or navigate to its existing location.
##### Option 1
To copy the folder from the downloaded repository:
```
cp -R /path_where_glim_will_download/config ./config
```
##### Option 2
Or, if you are already in the directory where the config folder is located:
```
cd /media/aisl2/aisl_data/docker/glim_docker
```
### 5 Run the Docker Container:
Start the Docker container from the pulled image with GPU and DISPLAY support. This command also mounts the config folder to the container and runs the glim_rosnode ROS2 node.

```
sudo docker run \
  -it \
  --rm \
  --net=host \
  --ipc=host \
  --pid=host \
  --gpus all \
  -e DISPLAY \
  -e ROS_DOMAIN_ID \
  -v $(realpath config):/glim/config \
  koide3/glim_ros2:humble_cuda12.2 \
  ros2 run glim_ros glim_rosnode --ros-args -p config_path:=/glim/config
  ```
  
### 6 Access the Running Docker Container:
Find the container ID and access the running Docker container to interact with its command line.
To find the container ID:
```
sudo docker ps
```
To access the container (replace <CONTAINER_ID> with the actual ID):
```
sudo docker exec -it <CONTAINER_ID> bash
```

### 7 Set Up the ROS Environment:
Inside the Docker container, source the ROS setup files to configure the ROS environment. This makes ROS commands available for use.
```
source /opt/ros/humble/setup.bash
source ~/ros2_ws/install/setup.bash
```
### 8 Run the ROS2 Node:
Execute the GLIM ROS2 node to start processing with the provided configuration.
```
ros2 run glim_ros glim_rosnode
```

### 9 Download and Prepare the ROS2 Bag File:
a) Download the ROS2 bag file 
 Download the ROS2 bag file from the provided link "https://koide3.github.io/glim/quickstart.html", extract it, and obtain the .db3 file. This file contains sample data for testing.
b) Copy the Bag File to the Docker Container:
Transfer the downloaded and extracted .db3 bag file from your local machine to the Docker container. This step is needed for the ROS2 node to access the data.
To copy the file (replace <CONTAINER_ID> with the actual container ID):
```
sudo docker cp /path_where_file_downloaded/os1_128_01_downsampled.db3 <CONTAINER_ID>:/root/ros2_ws/src/
# in my case
sudo docker cp /media/aisl2/aisl_data/docker/glim_docker/os1_128_01_downsampled/os1_128_01_downsampled.db3 <CONTAINER_ID>:/root/ros2_ws/src/
```
### 10 Play the Bag File Inside the Docker Container:
Inside the Docker container, navigate to the directory where the bag file was copied and play it to start processing the
```
cd /root/ros2_ws/src/
ros2 bag play os1_128_01_downsampled.db3
```
