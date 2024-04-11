# COLMAP Installation Directions With NVIDIA-GPU Support

```console
sudo apt-get purge nvidia*
sudo apt remove nvidia-*
sudo rm /etc/apt/sources.list.d/cuda*
sudo apt-get autoremove && sudo apt-get autoclean
sudo rm -rf /usr/local/cuda*

sudo ubuntu-drivers install
sudo reboot

sudo apt-get install g++ freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev

# download nvidia toolkit (e.g: 11.8 ->cuda_11.8.0_520.61.05_linux) as run file
# Continue in case of warning. Afterwards select only toolkit, when following directions in run file

sudo sh cuda_11.8.0_520.61.05_linux.run

echo 'export PATH=/usr/local/cuda-11.8/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda-11.8/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
sudo ldconfig

# install cudnn from (https://developer.nvidia.com/rdp/cudnn-archive), e.g: 8.6 -> cudnn-linux-x86_64-8.6.0.163_cuda11-archive

tar xf cudnn-linux-x86_64-8.6.0.163_cuda11-archive.tar.xz

mv cudnn-linux-x86_64-8.6.0.163_cuda11-archive cuda
sudo cp -P cuda/include/cudnn*.h /usr/local/cuda-11.8/include
sudo cp -P cuda/lib64/libcudnn* /usr/local/cuda-11.8/lib64/
sudo chmod a+r /usr/local/cuda-11.8/lib64/libcudnn*

sudo nano /home/username/.bashrc
# Insert in the end of the config file
export PATH="/usr/local/cuda-11.8/bin:$PATH"



# COLMAP

# first of all if conda is installed, and is active, then deactivate it
conda deactivate


sudo apt install git \
    cmake \
    libboost-program-options-dev \
    libboost-filesystem-dev \
    libboost-graph-dev \
    libboost-regex-dev \
    libboost-system-dev \
    libboost-test-dev \
    libeigen3-dev \
    libsuitesparse-dev \
    libfreeimage-dev \
    libgoogle-glog-dev \
    libgflags-dev \
    libglew-dev \
    qtbase5-dev \
    libqt5opengl5-dev \
    libcgal-dev

sudo apt install git build-essential

# install gcc-9 and gc++-9
sudo apt install g++-9 gcc-9


sudo apt install libatlas-base-dev libsuitesparse-dev
cd ..
git clone https://ceres-solver.googlesource.com/ceres-solver
cd ceres-solver
git checkout 2.0.0   # Check out the latest release.
mkdir build-2.0.0
cd build-2.0.0
cmake .. -DBUILD_TESTING=OFF -DBUILD_EXAMPLES=OFF
make -j3
sudo make install

sudo apt-get install libflann-dev
sudo apt-get install libsqlite3-dev
sudo apt-get install libmetis-dev

# find the compute architecture of gpu via (multiply the result by 10):
nvidia-smi --query-gpu=compute_cap --format=csv

compute_cap=$(nvidia-smi --query-gpu=compute_cap --format=csv | tail -n 1 | cut -d ',' -f 2 | tr -d ' ')
arch=$(echo "$compute_cap * 10 / 1" | bc)

cd
git clone https://github.com/colmap/colmap.git
cd colmap
git checkout dev
mkdir build
cd build

CC=/usr/bin/gcc-9 CXX=/usr/bin/g++-9 cmake .. -DCMAKE_CUDA_ARCHITECTURES="${arch}"
make -j3
sudo make install



# to verify that colmap is installed with cuda (essential for dense resconstruction), it is documented here:
colmap -h
```




# References:
- https://askubuntu.com/questions/1510137/error-installing-nvidia-drivers-on-ubuntu-22-04
- https://areong.medium.com/build-colmap-on-ubuntu-20-10-809b1f9ed4cf
- https://gist.github.com/primus852/b6bac167509e6f352efb8a462dcf1854
- https://velog.io/@jhjangjh/Ubuntu-20.04-NVIDIA-CUDA-11.6-%EB%B0%8F-cuDNN-8.5.0-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0


# References Regarding changing nvcc compiler and cudatoolkit version within conda environment (Handy for Running Deep Learning Repositories)
- https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#conda-installation
- https://anaconda.org/nvidia/cuda-nvcc
- https://medium.com/@jeanpierre_lv/installing-pytorch-with-gpu-support-on-ubuntu-a-step-by-step-guide-38dcf3f8f266
- https://dwijaybane.wordpress.com/2022/04/16/installing-multiple-cudatoolkit-on-ubuntu-using-conda/









    




