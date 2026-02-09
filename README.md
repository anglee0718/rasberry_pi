# 깃허브 문법
> * **[Markdown_Github](https://gist.github.com/ihoneymon/652be052a0727ad59601)**
- - -

# 라즈베리파이 초기 설정
```
sudo apt update
sudo apt upgrade
```
### * 한글깨짐
```
sudo apt-get install fonts-unfonts-core -y
sudo apt-get install ibus ibus-hangul -y
sudo reboot
```

# 라즈베리파이 한글
> * **[hangul](https://m.blog.naver.com/mythee1/223300805398)**
```
sudo apt update
sudo apt upgrade
sudo apt install ibus ibus-hangul
sudo apt install fonts-nanum fonts-unfonts-core
reboot
```

### 재시작
```
reboot
```
### vim 파일 열기
```
vi [파일명]
```
>  > * **vim의 저장및 종료 = :wq**

### nano 파일 열기
```
nano [파일명]
```

# InfluxDB 설치
* InfluxDB download key using wget
```
wget -q https://repos.influxdata.com/influxdata-archive_compat.key
echo '393e8779c89ac8d958f81f942f9ad7fb82a25e133faddaf92e15b16e6ac9ce4c influxdata-archive_compat.key' | sha256sum -c && cat influxdata-archive_compat.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdata-archive_compat.gpg > /dev/null
echo 'deb [signed-by=/etc/apt/trusted.gpg.d/influxdata-archive_compat.gpg] https://repos.influxdata.com/debian stable main' | sudo tee /etc/apt/sources.list.d/influxdata.list
```
* Packages are up to data && install Influxdb
```
 sudo apt-get update && sudo apt-get install influxdb -y
```
* InfluxDB as a background service on startup
```
sudo service influxdb start
```
*InfluxDB is status (service)
```
$ influx

>create database <데이터베이스이름>
확인 : show databases 
```
# Grafana Installation
## 1. Install the prerequisite packages
```
sudo apt-get install -y apt-transport-https software-properties-common wget
```
## 2. Import the GPG key:
```
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
```
## 3. To add a repository for stable releases, run the following command:
```
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```
## 4. Run the following command to update the list of available packages:
```
sudo apt-get update && sudo apt-get install grafana -y
```
## 5. Run the following command to server start
```
sudo systemctl start grafana-server
```
## influxdb import with python
```
pip install influxdb
```
* hint: See PEP 668 for the detailed specification.
```
  sudo rm /usr/lib/python3.11/EXTERNALLY-MANAGED
```
#TelegramBot
```
  pip install python-telegram-bot --upgrade
  git clone https://github.com/python-telegram-bot/python-telegram-bot
  pip install python-telegram-bot[job-queue] --pre
```
## TelegramBot Docs
* [https://docs.python-telegram-bot.org/en/stable/telegram.ext.jobqueue.html](https://docs.python-telegram-bot.org/en/stable/telegram.ext.jobqueue.html)
## PI 카메라 연결
* Legacy Camera disable
```
  libcamera-hello -t 0
```
* Python Lib 설치
```
  pip install picamera2
```
* Error
```
libEGL warning : DRI2: failed to authenticate
Made X/EGL preview window
[1773] INFO Camera camera_manager.cpp:297 libcamera v0.0.5+83-bde9b04f
ERROR: *** no cameras available ***
```
* 참고
```
  https://github.com/raspberrypi/picamera2/blob/main/examples/capture_png.py
```

# 언어별 확장자
* c언어
```
[변수명].c
```

* c++ 언어
```
[변수명].cpp
```

* java 언어
```
[변수명].java
```

* javascript 언어
```
[변수명].js
```

* c#언어
```
[변수명].cs
```

# 넥스트클라우드 앱 비번
```
gZNCE-XRPw7-A9yQD-ciLZP-48snm
```
```
import os
import random
import time
import tkinter as tk
from PIL import Image, ImageTk

PHOTO_DIR = "/home/pi/frame_photos"
SLIDE_SECONDS = 5

ADMIN_PASSWORD = "0718"  # 바꾸고 싶으면 여기 변경


class DigitalFrame:
    def __init__(self, root):
        self.root = root
        self.root.title("Digital Frame")
        self.root.attributes("-fullscreen", True)
        self.root.configure(bg="black")

        self.label = tk.Label(root, bg="black")
        self.label.pack(fill="both", expand=True)

        self.photos = []
        self.current_index = 0
        self.running = True

        self.root.bind("<Escape>", self.open_admin_mode)

        self.load_photos()
        self.show_next_photo()

    def load_photos(self):
        if not os.path.exists(PHOTO_DIR):
            os.makedirs(PHOTO_DIR)

        self.photos = [
            os.path.join(PHOTO_DIR, f)
            for f in os.listdir(PHOTO_DIR)
            if f.lower().endswith((".jpg", ".jpeg", ".png", ".gif", ".bmp"))
        ]

        random.shuffle(self.photos)

    def show_next_photo(self):
        if not self.running:
            return

        if not self.photos:
            self.label.config(
                text="사진이 없습니다.\nNextcloud 동기화를 확인하세요.",
                fg="white",
                font=("Arial", 40),
            )
            self.root.after(3000, self.show_next_photo)
            return

        photo_path = self.photos[self.current_index]

        try:
            img = Image.open(photo_path)
            screen_w = self.root.winfo_screenwidth()
            screen_h = self.root.winfo_screenheight()

            img.thumbnail((screen_w, screen_h))
            tk_img = ImageTk.PhotoImage(img)

            self.label.config(image=tk_img, text="")
            self.label.image = tk_img

        except Exception as e:
            print("이미지 오류:", e)

        self.current_index = (self.current_index + 1) % len(self.photos)
        self.root.after(SLIDE_SECONDS * 1000, self.show_next_photo)

    def open_admin_mode(self, event=None):
        self.running = False

        admin = tk.Toplevel(self.root)
        admin.title("관리자 모드")
        admin.geometry("400x300")
        admin.configure(bg="gray20")

        tk.Label(admin, text="관리자 비밀번호:", fg="white", bg="gray20", font=("Arial", 14)).pack(pady=10)
        pw_entry = tk.Entry(admin, show="*", font=("Arial", 14))
        pw_entry.pack(pady=10)

        msg = tk.Label(admin, text="", fg="red", bg="gray20", font=("Arial", 12))
        msg.pack()

        def check_pw():
            if pw_entry.get() == ADMIN_PASSWORD:
                msg.config(text="성공!", fg="lime")
                show_admin_menu()
            else:
                msg.config(text="비밀번호 틀림", fg="red")

        def show_admin_menu():
            for widget in admin.winfo_children():
                widget.destroy()

            tk.Label(admin, text="관리자 메뉴", fg="white", bg="gray20", font=("Arial", 16)).pack(pady=10)

            def sync_now():
                os.system("/home/pi/sync_photos.sh &")

            def reload_photos():
                self.load_photos()
                self.current_index = 0

            def exit_admin():
                admin.destroy()
                self.running = True
                self.show_next_photo()

            def shutdown_pi():
                os.system("sudo shutdown -h now")

            tk.Button(admin, text="사진 동기화 실행", font=("Arial", 14), command=sync_now).pack(pady=5)
            tk.Button(admin, text="사진 다시 불러오기", font=("Arial", 14), command=reload_photos).pack(pady=5)
            tk.Button(admin, text="라즈베리파이 종료", font=("Arial", 14), command=shutdown_pi).pack(pady=5)
            tk.Button(admin, text="닫기", font=("Arial", 14), command=exit_admin).pack(pady=5)

        tk.Button(admin, text="확인", font=("Arial", 14), command=check_pw).pack(pady=10)


if __name__ == "__main__":
    root = tk.Tk()
    app = DigitalFrame(root)
    root.mainloop()

```
```
#sync...코드
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import os
import time
import requests
import sys
from requests.auth import HTTPBasicAuth
from xml.etree import ElementTree as ET
#http://localhost:8080/remote.php/dav/files/anglee0718 or http://localhost:8080/remote.php/webdav/frame_photos/
WEBDAV_URL = "http://localhost:8080/remote.php/dav/files/anglee0718/frame_photos/"
USERNAME = "anglee0718"
PASSWORD = "gZNCE-XRPw7-A9yQD-ciLZP-48snm"

headers = {"Depth": "1"}

LOCAL_DIR = "/home/pi/nc_photos"


def download_photos():
    try:
        response = requests.request("PROPFIND", WEBDAV_URL, auth=HTTPBasicAuth(USERNAME, PASSWORD))

        if response.status_code != 207:
            print("PROPFIND 실패:", response.status_code)
            return

        tree = ET.fromstring(response.content)
        namespace = {'d': 'DAV:'}

        for elem in tree.findall('.//d:href', namespace):
            file_path = elem.text
            file_name = os.path.basename(file_path)

            if not file_name.lower().endswith((".jpg", ".jpeg", ".png")):
                continue

            local_file = os.path.join(LOCAL_DIR, file_name)

            if os.path.exists(local_file):
                continue

            file_url = WEBDAV_URL + file_name

            r = requests.get(file_url, auth=HTTPBasicAuth(USERNAME, PASSWORD))
            if r.status_code == 200:
                with open(local_file, "wb") as f:
                    f.write(r.content)
                print("Downloaded:", file_name)

    except Exception as e:
        print("Error:", e)


def main_loop():
    if not os.path.exists(LOCAL_DIR):
        os.makedirs(LOCAL_DIR)

    while True:
        download_photos()
        time.sleep(60)


if __name__ == "__main__":
    if "--once" in sys.argv:
        download_photos()
    else:
        main_loop()
```
