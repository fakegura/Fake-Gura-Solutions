This is a working CTRL+V installation for PuTTY without GPU
```
sudo apt-get update
yes | sudo apt-get install curl
yes | sudo apt-get install -y libportaudio2
yes | sudo apt install build-essential
yes | sudo add-apt-repository ppa:ubuntu-toolchain-r/test
yes | sudo apt-get install --only-upgrade libstdc++6
cd /tmp
curl -O https://repo.anaconda.com/archive/Anaconda3-2024.10-1-Linux-x86_64.sh
bash Anaconda3-2024.10-1-Linux-x86_64.sh -b
cd /home/azureuser/
anaconda3/bin/conda init
source ~/.bashrc
yes | conda create -n vcclient-dev python=3.10
conda activate vcclient-dev
cp /usr/lib/x86_64-linux-gnu/libstdc++.so.6 /home/azureuser/anaconda3/envs/vcclient-dev/lib/
pip install pip==24.0
pip install fairseq
pip install pyworld
git clone https://github.com/w-okada/voice-changer.git
cd /home/azureuser/voice-changer/server
pip install -r requirements.txt
server_ip=$(curl -s http://ifconfig.me)
python3 MMVCServerSIO.py -p 18888 --https true \
    --content_vec_500 pretrain/checkpoint_best_legacy_500.pt  \
    --content_vec_500_onnx pretrain/content_vec_500.onnx \
    --content_vec_500_onnx_on true \
    --hubert_base pretrain/hubert_base.pt \
    --hubert_base_jp pretrain/rinna_hubert_base_jp.pt \
    --hubert_soft pretrain/hubert/hubert-soft-0d54a1f4.pt \
    --nsf_hifigan pretrain/nsf_hifigan/model \
    --crepe_onnx_full pretrain/crepe_onnx_full.onnx \
    --crepe_onnx_tiny pretrain/crepe_onnx_tiny.onnx \
    --rmvpe pretrain/rmvpe.pt \
    --model_dir model_dir \
    --samples samples.json \
    --host 0.0.0.0 \
    --allowed-origins https://$server_ip:18888 
```

Installation with GPU.
Same as above but without starting the server:
```
sudo apt-get update
yes | sudo apt-get install curl
yes | sudo apt-get install -y libportaudio2
yes | sudo apt install build-essential
yes | sudo add-apt-repository ppa:ubuntu-toolchain-r/test
yes | sudo apt-get install --only-upgrade libstdc++6
cd /tmp
curl -O https://repo.anaconda.com/archive/Anaconda3-2024.10-1-Linux-x86_64.sh
bash Anaconda3-2024.10-1-Linux-x86_64.sh -b
cd /home/azureuser/
anaconda3/bin/conda init
source ~/.bashrc
yes | conda create -n vcclient-dev python=3.10
conda activate vcclient-dev
cp /usr/lib/x86_64-linux-gnu/libstdc++.so.6 /home/azureuser/anaconda3/envs/vcclient-dev/lib/
pip install pip==24.0
pip install fairseq
pip install pyworld
git clone https://github.com/w-okada/voice-changer.git
cd /home/azureuser/voice-changer/server
pip install -r requirements.txt 
```

NVIDIA drivers installation according to Azure manual.
Drivers:
```
sudo apt install -y ubuntu-drivers-common
sudo ubuntu-drivers install
sudo reboot
```

and CUDA:
```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo apt install -y ./cuda-keyring_1.1-1_all.deb
sudo apt update
sudo apt -y install cuda-toolkit-12-5
sudo reboot
```

Starting the server:
```
cd /home/azureuser/voice-changer/server
conda activate vcclient-dev
server_ip=$(curl -s http://ifconfig.me)
python3 MMVCServerSIO.py -p 18888 --https true \
    --content_vec_500 pretrain/checkpoint_best_legacy_500.pt  \
    --content_vec_500_onnx pretrain/content_vec_500.onnx \
    --content_vec_500_onnx_on true \
    --hubert_base pretrain/hubert_base.pt \
    --hubert_base_jp pretrain/rinna_hubert_base_jp.pt \
    --hubert_soft pretrain/hubert/hubert-soft-0d54a1f4.pt \
    --nsf_hifigan pretrain/nsf_hifigan/model \
    --crepe_onnx_full pretrain/crepe_onnx_full.onnx \
    --crepe_onnx_tiny pretrain/crepe_onnx_tiny.onnx \
    --rmvpe pretrain/rmvpe.pt \
    --model_dir model_dir \
    --samples samples.json \
    --host 0.0.0.0 \
    --allowed-origins https://$server_ip:18888
```

Installing GUI:
```
sudo DEBIAN_FRONTEND=noninteractive apt-get -y install xfce4
sudo apt install xfce4-session
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
sudo adduser xrdp ssl-cert
echo xfce4-session >~/.xsession
sudo systemctl restart xrdp
```

To use RDP, set a password:
```
sudo passwd azureuser
```

Download Gura:
```
cd /home/azureuser/
mkdir rvc-models
cd rvc-models
wget https://huggingface.co/Gigrig/GigrigRVC/resolve/main/GawrGura_RVC_v2_Itaila_e200_s92600.zip
```
In Network Security you have to open 3389 port for anything inbound for RDP.
