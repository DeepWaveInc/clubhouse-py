# Clubhouse Crawler

## 需求
- 抓取現有的Channel list
- 抓取Channel Topic
- 錄製Channel 音訊


## Possible Method

### 1. Clubhouse API Written in Python(3rd party)
- Github repo: https://github.com/stypr/clubhouse-py
- Environments: Windows/OSX(由於Agora Python SDK尚未支援)
    - Dependency: Python 3.7 or higher

註：
- [Issue: Agora-Python-SDK: Linux support](https://github.com/AgoraIO-Community/Agora-Python-SDK/issues/1)

### 小結：
- 需要準備一隻手機號碼註冊以及已經加入Clubhouse
- 在Windwos/OSX上測試cli.py結果
    - 可以登入/抓取Channel list(似乎無法獲取全部的Channel)
    - 可以截取Channel Speaker list
    - 可以聽Channel

- OSX可以成功啟動agora sdk的錄音 ``` RTC.startAudioRecording("%s.mp3"%channel_name, 32)``` 以此錄製channel中的音訊
-  使用Windwos 錄製沒有成功。
    -  針對Windows可能需要另尋方法以擷取Output device 的Audio Stream
        - Use pyaudio to record the system audio
            - https://stackoverflow.com/questions/26573556/record-speakers-output-with-pyaudio
            - https://github.com/AgoraIO-Community/Agora-Python-QuickStart/blob/master/capture_audio_data_to_audio_file/capture_audio_data_to_audio_file.py
        - 目前測試後可以透過此repo prebuild pyaudio/以及Windows WASAPI 錄製下檔案(請見[sec_01](#sec_01)

註:
- 若以他台機器登入帳號後，若需要重新以目前此台登入，此api記錄下的```setting.ini```需刪除，並重新輸入手機號碼，再經過簡訊碼驗證才得以使用。
---
### An example to crawl the channel list and recod the audio
Other requirements:
- A clubhouse account(phone number) for authentication using


##### Installation
0. Creat a python env with python 3.7 or higher
1. Clone the project
```
$ git clone https://github.com/stypr/clubhouse-py.git clubhouse
$ cd clubhouse
```
2. Install dependencies 
```
$ pip3 install -r requirements.txt
```
3. Install Agora SDK for vocie communication
Refer to [Agora-Python-SDK#installation](https://github.com/AgoraIO-Community/Agora-Python-SDK#installation)
```
$pip3 install agora-python-sdk
```
Note: Only support for 64-bit OS. For 32-bit OS, please refer to Method 2.

##### Usage
0. Add a line of code for recoring the channle audio usage to the sample code [```cli.py```](https://github.com/stypr/clubhouse-py/blob/5d4627f9fad4f689aafb8300a1c7086b74c6cb16/cli.py) of the 3rd party clubhouse api repo ```clubhouse-py```

The line of the code for recording use(part of argora api):
```=python
RTC.startAudioRecording ("%s.mp3"%channel_name, 32)
```
- .startAudioRecording(filePath,sampleRate)
    - the default sample rate: 32k


The revision part  ```cli.py``` of the 3rd party clubhouse api repo ```clubhouse-py```

```=python=253
        # Check for the voice level.
        if RTC:
            token = channel_info['token']
            RTC.joinChannel(token, channel_name, "", int(user_id))
            RTC.startAudioRecording ("%s.mp3"%channel_name, 32)
```
Ref:
- [How to use Agora SDK for Recording multiple Audio channels simultaneously](https://stackoverflow.com/questions/66951670/how-to-use-agora-sdk-for-recording-multiple-audio-channels-simultaneously)
- [Agora C++ API](https://docs.agora.io/en/Video/API%20Reference/cpp/v3.1.2/classagora_1_1rtc_1_1_i_rtc_engine.html#a3c05d82c97a9d63ebda116b9a1e5ca3f)

1. Running a standalone client
```
$ python cli.py
```

- login with your registerd acoount(phone number)
    - Input the SMS Code
![](https://i.imgur.com/XciGIrc.png)

After login, it would get a list about the channel. 
![](https://i.imgur.com/EHMPBKL.jpg)


![](https://i.imgur.com/7kBJnrW.jpg)

![](https://i.imgur.com/lQei0Rc.png)

---
## How to record the Clubhouse Audio on Windows and save to a file?
<a id='sec_01'></a>

問題
- 目前在Windows上雖也可以執行Clubhouse API，會遇到錄音程式有啟動但無錄到的情形。

方法:
1. 找出agora python sdk在windows的問題
2. 錄下Windows的系統輸出聲音

### 方法2: 錄下Windows的系統輸出聲音

- 使用他人改過的pyaudio [[1]](https://github.com/intxcc/pyaudio_portaudio)
    - Support of Windows sound loopback: Record the output of your soundcard

#### Step
1. Install the prebuild pyaudio from the forked repo.
  - [prebuild pyaudio](https://github.com/intxcc/pyaudio_portaudio/releases/download/1.1.1/PyAudio-0.2.11-cp37-cp37m-win_amd64.whl)

How to install?
```
$pip install PyAudio-0.2.11-cp37-cp37m-win_amd64.whl
```
2. Activate the sample code of the repo after run the clubhouse cli.py

The sample code of the repo is a script to choose record which system audio channel.

- [Sample code](https://github.com/intxcc/pyaudio_portaudio/blob/0713e32268633365e3a77ab191024eb26d4ec8d9/example/echo_python3.py)

- It is downloaded at ```record.py```

執行後會詢問選擇哪個系統device，需要選擇Clubhouse目前聲音輸出所使用的output device 
![](https://i.imgur.com/htk2YMD.png)


註:
- 記得將Sample code 中的[Setchannel](https://github.com/intxcc/pyaudio_portaudio/blob/0713e32268633365e3a77ab191024eb26d4ec8d9/example/echo_python3.py#L105) 改為1，預設會輸出為雙聲道，生成的檔案大小會比較大。


Ref:
- [1][The repo Support record Windows output sound](https://github.com/intxcc/pyaudio_portaudio)

