# aiarm
자율주행하기에 앞서 jetson, notebook을 동일한 AP에 연결해야합니다. 192.168.55.x를 사용하는 USB 연결을 주행에서는 사용할 수 없기 때문입니다. terminal에서 아래 방법으로 AP에 연결할 수 있습니다.
# 실행해야할 명령
$ sudo nmcli device wifi list
$ sudo nmcli device wifi connect <ssid_name> password <password>
$ ifconfig

# 명령어 실행 예제
# password가 없는 경우
zeta@jp461:~$ sudo nmcli device wifi connect STARTUP_LOUNGE_love
Device 'wlan0' successfully activated with 'e1d72d88-360a-468b-8487-3cf3ef5b343c'.
zeta@jp461:~$ ifconfig
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
:
wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.37.160  netmask 255.255.255.0  broadcast 192.168.37.255
        inet6 fe80::89c:53da:d58:afc1  prefixlen 64  scopeid 0x20<link>
        ether 00:28:f8:7d:61:03  txqueuelen 1000  (Ethernet)
        RX packets 734  bytes 1018170 (1.0 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 350  bytes 35354 (35.3 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

# password가 있는 경우
jetson@jp4512G:~$ sudo nmcli device wifi connect U+NetD681 password xxxxxxx
Device 'wlan0' successfully activated with '51523154-f356-461f-b8bb-54e1ac21bd10'.
jetson@jp4512G:~$ ifconfig wlan0
wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.219.107  netmask 255.255.255.0  broadcast 192.168.219.255
        inet6 fe80::21d:8403:c491:2b80  prefixlen 64  scopeid 0x20<link>
        ether 00:e0:4b:af:07:1f  txqueuelen 1000  (Ethernet)
        RX packets 6110  bytes 9029131 (9.0 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 3666  bytes 340490 (340.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

# 연결을 해제할 때
$ sudo nmcli device disconnect wlan0

#명령 실행 로그
jetson@jp4512G:~/catkin_ws$ sudo nmcli device disconnect wlan0
Device 'wlan0' successfully disconnected.





3선 쿨링팬은 바로그 동작합니다만, 4선 PWM 쿨링팬은 아래 작업을 해야 동작합니다. 즉 PWM 신호에 출력을 주어야 동작하는 것입니다. CPU 사용에 따라 쿨링팬 속도를 자동으로 조정하는 utility를 설치하는게 좋습니다.
cd Downloads
git clone https://github.com/jetsonworld/jetson-fan-ctl.git
cd jetson-fan-ctl

sudo sh install.sh
​
실행 로그는 아래와 같습니다.
jetson@jp4512G:~$ cd Downloads/
jetson@jp4512G:~/Downloads$ git clone https://github.com/jetsonworld/jetson-fan-ctl.git
Cloning into 'jetson-fan-ctl'...
remote: Enumerating objects: 90, done.
remote: Counting objects: 100% (90/90), done.
remote: Compressing objects: 100% (83/83), done.
remote: Total 90 (delta 40), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (90/90), done.
jetson@jp4512G:~/Downloads$ cd jetson-fan-ctl/
jetson@jp4512G:~/Downloads/jetson-fan-ctl$ sudo sh install.sh 
[sudo] password for jetson: 
install.sh: 4: [: !=: unexpected operator
settling to /usr/local/bin/automagic-fan/...
done
adding service to /lib/systemd/system/...
done
creating config at /etc/automagic-fan/
done
starting and enabling service...
Created symlink /etc/systemd/system/multi-user.target.wants/automagic-fan.service → /lib/systemd/system/automagic-fan.service.
done
automagic-fan installed sucessfully!

To configure, edit /etc/automagic-fan/config.json (needs sudo)



1.3 VNC설치
VNC는 다음과 같은 경우 반드시 필요합니다. 만일을 위해 설치해두는게 좋습니다.
ROS remote 환경 설정을 만들지 못했을 때
Jetson에 TV를 연결할 수 없을 때
젯슨 나노는 화면 공유를 바로 허용하지 않게 되어있습니다. VNC를 사용해서 노트북에서 젯슨 화면을 보면서 작업할 경우 아래 글을 따라서 VNC 서버를 동작하게 해주십시오.
https://developer.nvidia.com/embedded/learn/tutorials/vnc-setup
# Jetson Nano 2GB(running LXDE)
mkdir -p ~/.config/autostart
cp /usr/share/applications/vino-server.desktop ~/.config/autostart/.
# Jetson Nano 4GB (running GNOME)
cd /usr/lib/systemd/user/graphical-session.target.wants
sudo ln -s ../vino-server.service ./.

# 공통
gsettings set org.gnome.Vino prompt-enabled false
gsettings set org.gnome.Vino require-encryption false
gsettings set org.gnome.Vino authentication-methods "['vnc']"
# 아래 passord에 본인이 사용할 비밀번호로 변경하세요
gsettings set org.gnome.Vino vnc-password $(echo -n 'passsword'|base64)

# example, password is jetson
# gsettings set org.gnome.Vino vnc-password $(echo -n 'jetson'|base64)

 1. jessiarm install on Jetson
$ cd ~/catkin_ws/src
$ git clone https://github.com/zeta0707/jessiarm.git
$ cd ~/catkin_ws


### 2. **jessiarm install on PC**

먼저 melodic-desktop-full을 설치합니다. 그 후 젯슨과 동일하게 jessiarm git을 clone 합니다. 그 후 빌드합니다.
