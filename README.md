# A subcortical segmentation tool based on anatomy attentional fusion network for developing macaques

<div align=center><img width="608" height="601" src="https://github.com/TaoZhong11/Macaque_subcortical_segmentation/blob/main/Overview.jpg"/></div>


Considering that most macaque MRI samples usually contain T1w but not necessarily T2w, the model disclosed here supports inputting only T1w, and then generates corresponding SDM, subcortical region, and six types of subcortical fine structures(**thalamus, caudate, putamen, pallidum, hippocampus, and amygdala**).



## How to run

### Install Docker
Please refer to the official installation guideline [Install Docker on Linux](https://docs.docker.com/desktop/install/linux-install/). You can follow the below commands to install the Docker on the popular Ubuntu Linux system. If you have previously installed Docker on your computer, please skip this step.
```
sudo apt update
sudo apt install ca-certificates curl gnupg lsb-release
sudo apt mkdir -p /etc/share/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/share/keyrings/docker.gpg
sudo echo "deb [arch=$(dpkg --print-architechture) signed-by=/etc/share/keyrings/docker.png] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
Since the docker needs the `sudo` to run the general commands, to run without `sudo`, you need to add your user name `${USER}` to the docker group, which can be done
```
sudo group add docker
sudo usermod -aG docker ${USER}
```
After running these commands, you may need to restart your computer to make the configuration take effect. One easy way to check whether you have successfully installed the Docker or not is to run the Docker using the command ```docker info```, which dose not need ```sudo``` requirement if you have added your username to the `docker` group. Please refer to [more details](https://docs.docker.com/get-started/) for potential issues on Docker.


### Install Nvidia-docker
The ```Nvidia-docker``` is required since our pipeline needs GPU support. Please refer to the official installation guideline [Install Nvidia-docker on Linux](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker). If you have previously installed Nvidia-docker, please skip this step. The following commands can be used to install the Nvidia-docker on the popular Ubuntu Linux system.
```
sudo apt update
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-docker.gpg
distribution=$(. /etc/os-release; echo ${ID}${VERSION_ID})
curl -sL https://nvidia.github.io/nvidia-docker/libnvidia-container/${distribution}/libnvidia-container.list | sed 's#deb https://# deb [signed-by=/usr/share/keyrings/nvidia-docker.gpg] https://#g' | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt update
sudo apt install -y nvidia-docker2
```
After the installation, please restart the Docker daemon to complete the installation after setting the default funtime:
```
sudo systemctl restart docker
```
Finally, you can check whether you have successfully installed the ```Nvidia-docker``` using the following command:
```
docker run --rm --gpus=all nvidia/cuda:9.0-base nvidia-smi
```
If succeeded, the output should be the GPU card information on your PC. 

### Download the pipeline

Run 
```
docker pull wxyabc/nbest:1.3
```

After downloading, you can use ```docker images``` to see the container images you have downloaded.



### Try your data ###
Create a directory, e.g., named ```Macaque_testing_scans```, which will be mounted to the container. The T1w scans that need to be processed should be in ```Macaque_testing_scans/data/xxxxx.nii.gz```. 
Run by a new terminal:
```
docker run -it --gpus=all --ipc=host -v /absolute/path/to/Macaque_testing_scans:/workspace/demo/  wxyabc/nbest:1.3
cp nBEST_subcortical_SDM.py demo/nBEST_subcortical_SDM.py
cd demo
```
If your data are raw data with skull, run
```
python nBEST_subcortical_SDM.py
```
And if you want to process your data without brain extraction (which means your input should be brain images without skull),  run
```
python nBEST_subcortical_SDM.py -skip_be
```

### Output illustration ###
Upon completion of the processing, all resulting outputs will be generated and stored in the ```Macaque_testing_scans``` directory. The following explains what the results are: 
* ```brain_mask/```: The brain mask of raw image.
* ```brain_img/```: The skull stripped brain image.
*  ```brain_subcortical_6class/```:	The subcortical fine structures.

<div align=center><img width="748" height="727" src="https://github.com/TaoZhong11/Macaque_subcortical_segmentation/blob/main/visual_result.jpg"/></div>

