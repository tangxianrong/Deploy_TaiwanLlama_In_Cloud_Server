# 運行llama 

## 運行項目:
使用台智雲的虛擬運算服務。並用text-generation-inference部屬Taiwan-LLaMa，另外使用Taiwan-Llama中的demo程式作為UI。
### 資源
* https://github.com/MiuLab/Taiwan-LLaMa
* https://huggingface.co/yentinglin/Taiwan-LLaMa-v1.0
* https://github.com/huggingface/text-generation-inference


## 流程

### 1. 虛擬環境Ubuntu安裝nvidia driver
資源

* https://www.nvidia.com/content/DriverDownloads/confirmation.php?url=/tesla/535.86.10/NVIDIA-Linux-x86_64-535.86.10.run&lang=us&type=Tesla
* https://us.download.nvidia.com/tesla/535.86.10/NVIDIA-Linux-x86_64-535.86.10.run
* https://docs.twcc.ai/docs/concepts-tutorials/twcc/vcs/tutorials/install-nvidia-gpu-driver/

主要是依照台智雲(第三個連結)進行安裝。自己這邊是安裝535.86.10版本。

### 2. 安裝cuda
* https://jackfrisht.medium.com/install-nvidia-driver-via-ppa-in-ubuntu-18-04-fc9a8c4658b9

目前只有安裝到cuda，cudnn不用安裝。


### 3. 安裝docker和 NVIDIA Container Toolkit
* https://grady1006.medium.com/ubuntu18-04%E5%AE%89%E8%A3%9Ddocker%E5%92%8Cnvidia-docker-%E4%BD%BF%E7%94%A8%E5%A4%96%E6%8E%A5%E9%A1%AF%E5%8D%A1-1e3c404c517d
其中要測試有沒有成功透過docker抓到要注意以下問題
* https://blog.csdn.net/YoJayC/article/details/120209482


## 各式問題：

driver安裝方法走
* https://jackfrisht.medium.com/install-nvidia-driver-via-ppa-in-ubuntu-18-04-fc9a8c4658b9

安裝cuda後才能使用nvidia-smi
接著sudo apt install nvidia-cuda-toolkit
就能使用nvcc

* https://docs.docker.com/engine/install/ubuntu/#install-docker-ce
完成到Upgrade Docker Engine
接著跑這個
* https://grady1006.medium.com/ubuntu18-04%E5%AE%89%E8%A3%9Ddocker%E5%92%8Cnvidia-docker-%E4%BD%BF%E7%94%A8%E5%A4%96%E6%8E%A5%E9%A1%AF%E5%8D%A1-1e3c404c517d docker版本要改成相應的

有時重啟後會發生nvidia-smi抓不到硬體問題，這時要重新運行以下程式：
<pre>
<code>
#安裝對應版本的 kernel header
sudo apt-get install linux-headers-$(uname -r)
</code>
</pre>
至於為何要重新安裝問題待解



Makefile中有執行到Python 其中--nproc_per_node參數應該改為1，此代表每個節點的進程數，但llama模型應該無法在一台gpu上塞兩個進程



Encryptpoint >launcher >?
Makefile > server/sli


執行失敗可能跟硬體相關
https://github.com/huggingface/text-generation-inference/issues/424

也可能跟套件版本有關 protobuf == 3.19
https://github.com/huggingface/text-generation-inference/issues/447

執行失敗可以嘗試降低max_input_length和max_length，或可再嘗試降低max-total-tokens 會觀察到明明記憶體還有剩20幾G，只是要放入十幾MB也報OOM的情況，以上方法也能夠解決。


其他參考資源：
https://blog.csdn.net/Limenrence/article/details/130213073
https://ivonblog.com/posts/ubuntu-install-nvidia-drivers/ (這個少了nvidia driver的安裝 且原驅動nouveau禁用也不知有無成功，好像跟台智雲的教學有差異)
https://www.linuxprobe.com/ubuntu-nvidia-v100-gpu.html (也是自己執行失敗的教學，但可能是過程中有一些差異以及沒有安裝nvidia driver所致)


解決台智雲Permissions for ‘.pem‘ are too open問題
https://barebarge.blogspot.com/2018/06/sshkeypageant-server-refused-our-key.html
https://blog.csdn.net/qq_31375855/article/details/125845446  
https://superuser.com/questions/1678830/server-refused-our-key-only-from-mobaxterm-bookmark-setup

linux檢視硬體資訊指令
https://www.tokfun.net/os/linux/linux-hardware-information-command/

linux 系統操作指令(主要是查詢如何重啟)
https://www.runoob.com/w3cnote/linux-shutdown-reboot.html

其他嘗試：

本來打算在容器服務上安裝docker，但一來網路不推薦，二是在安裝過程中出現docker安裝失敗問題


其他方法：
https://m.5kcrm.com/46012

Nvidia driver 重啟后異常問題
https://askubuntu.com/questions/1356560/nvidia-drivers-suddenly-stopped-working-after-rebooting-from-windows-reinstall
