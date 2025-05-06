# 前言
**本篇文章在介紹如何在 Raspberry Pi 3 上面安裝 RTL-SDR Blog V4 並透過網路遠端存取**  
`僅限於 Raspberry Pi 3 + RTL-SDR Blog V4 的組合`  

**硬體:**  
Raspberry Pi 3 Model B  
RTL-SDR Blog V4 (以下簡稱V4)  

**作業系統:**  
Raspberry Pi OS with desktop  
System: 64-bit  
Kernel version: 6.1  
Debian version: 11 (bullseye)  

**建議:**  
有線網路(較推薦)或外接無線網卡，因為 Raspberry Pi 內建的無線網路效能真的是不太優  
USB 延長線，把 V4 拉出來避免干擾，以及可以在 V4 上面貼散熱片(真的很燙)  
外接天線，V4 內附的天線真的不太行，比收音機的還差  

# 安裝驅動
**🧱 系統安裝與更新** 
```bash
sudo apt update
sudo apt upgrade
sudo apt install git cmake build-essential libusb-1.0-0-dev
```
  
**📥 安裝 RTL-SDR Blog V4 的驅動** 
```bash
git clone https://github.com/rtlsdrblog/rtl-sdr-blog
cd rtl-sdr-blog
mkdir build
cd build
cmake .. -DINSTALL_UDEV_RULES=ON
make
sudo make install
sudo ldconfig
```
  
**✅ 設定 udev 規則（讓系統辨識 RTL-SDR）**
```bash
sudo cp ../rtl-sdr.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules
sudo udevadm trigger
```

**⛔ 移除預設 DVB-T 驅動（防止衝突）**
```bash
sudo bash -c 'echo "blacklist dvb_usb_rtl28xxu" >> /etc/modprobe.d/no-rtl.conf'
sudo bash -c 'echo "blacklist rtl2832" >> /etc/modprobe.d/no-rtl.conf'
sudo bash -c 'echo "blacklist rtl2830" >> /etc/modprobe.d/no-rtl.conf'
sudo update-initramfs -u
```

**執行完成後，需要重新開機**
```bash
sudo reboot
```

# 功能測試
**可以輸入測試指令**
```bash
rtl_test
```

**如果驅動有安裝成功，應該會看到類似這樣的輸出 (~~不要問我失敗會長甚麼樣，因為我一次就成功了~~)**
```yaml
Found 1 device(s):
  0:  Realtek, RTL2838UHIDIR, SN: 00000001

Using device 0: Generic RTL2832U OEM
...
```

# 安裝 Server 端
**📦 安裝 rtl_tcp**
上面的 rtl-sdr-blog 其實已經包含，但為了保險起見還是再確認一次  
```bash
sudo apt install rtl-sdr
```

**🚀 啟動 rtl_tcp**
```bash
rtl_tcp -a 0.0.0.0
```

**如果有啟動成功，應該會看到類似這樣的輸出**
```yaml
Found 1 device(s):
  0: Realtek, RTL2838UHIDIR, SN: 00000001
Using device 0: Generic RTL2832U OEM
...
listening at 0.0.0.0:1234
```

# Client端設定
打開 SDRSharp  
選擇裝置為：RTL-SDR (TCP)  
輸入IP位置後即可連線，Port維持預設的1234  






