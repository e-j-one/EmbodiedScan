## Build docker

```bash
docker build -t embodiedscan:test docker
```

Install dependency
```bash
docker run --gpus all -it --net=host \
        -e DISPLAY=$$DISPLAY \
        -v /tmp/.X11-unix:/tmp/.X11-unix \
        -v "${PWD}":/workspace \
        --name=embodiedscan \
        --ipc=host embodiedscan:test
```
Inside the docker container
```bash
python install.py all
```
Commit the contianer to the image.
```bash
docker commit embodiedscan embodiedscan:test_installed

docker stop embodiedscan
docker rm embodiedscan
```

## Train
```bash
docker run -it --rm -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$(DISPLAY) -e USER=$(USER) \
	-e runtime=nvidia -e NVIDIA_DRIVER_CAPABILITIES=all -e NVIDIA_VISIBLE_DEVICES=all \
	-e PYTHONPATH=/workspace \
	-v "${PWD}":/workspace \
	--shm-size 128G \
	--net host --gpus all --privileged --name embodiedscan embodiedscan:test_installed
```

Inside the docker container
```bash
python tools/train.py configs/occupancy/mv-occ_8xb1_embodiedscan-occ2x-11class.py --work-dir=work_dirs/mv-occ
```

# Changes
- Add config/occupancy/mv-occ_8xb1_embodiedscan-occ2x-11class.py
  - Change pc range, n_voxel, datset_type, class_names and ann_file path.
- Add embodiedscan/datasets/scannetpp_2x_dataset.py
  - sparse occ -> 3d mat
- Modify embodiedscan/datasets/__init__.py
  - Register Scannetpp2xDataset. 

# Notes
- Assumed that the visible_occupancy is already upsampled to 60, 60, 20