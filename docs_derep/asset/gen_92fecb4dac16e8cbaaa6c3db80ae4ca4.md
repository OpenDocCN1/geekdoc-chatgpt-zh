[PRE0]python

import pyaudio

import wave

# 配置参数

TEMP_AUDIO_FOLDER = "temp_audio"  # 临时存放录音的目录
RATE = 16000  # 每秒采样点数
CHANNELS = 1  # 声道数量为1即单声道
FORMAT = pyaudio.paInt16  # 采样编码为16位有符号整数
RECORD_SECONDS = 5  # 单次录制片段长度（秒）
CHUNK = 1024  # 单次读取的音频帧字节数

[PRE1]python

def record_audio(audio_queue, stop_event):

    """负责采集音频并把文件路径推入队列"""

    p = pyaudio.PyAudio()

    stream = p.open(format=FORMAT,

                    channels=CHANNELS,

                    rate=RATE,

                    input=True,

                    frames_per_buffer=CHUNK)

    file_index = 0

    print("音频采集已启动")

    try:

        while not stop_event.is_set():

            frames = []

            for _ in range(0, int(RATE / CHUNK * RECORD_SECONDS)):

                if stop_event.is_set():

                    break

                data = stream.read(CHUNK)

                frames.append(data)

            if frames:

                temp_file = os.path.join(

                    TEMP_AUDIO_FOLDER, f"audio_{file_index}.wav"

                )

                with wave.open(temp_file, 'wb') as wf:

                    wf.setnchannels(CHANNELS)

                    wf.setsampwidth(p.get_sample_size(FORMAT))

                    wf.setframerate(RATE)

                    wf.writeframes(b''.join(frames))

                audio_queue.put(temp_file)

                file_index += 1

    finally:

        stream.stop_stream()

        stream.close()

        p.terminate()

        print("\n录音任务结束。")

[PRE2]python

import queue

import time

import sys

from dotenv import load_dotenv, find_dotenv

from openai import OpenAI

def transcribe_audio(audio_queue, stop_event):

    """取出队列里的音频并调用语音识别"""

    _ = load_dotenv(find_dotenv())

    client = OpenAI()

    model = "whisper-1"

    print("转录服务启动")

    try:

        while not stop_event.is_set():

            try:

                temp_file = audio_queue.get(timeout=1)

            except queue.Empty:

                continue

            if temp_file is None:

                continue

            try:

                with open(temp_file, "rb") as audio_file:

                    response = client.audio.transcriptions.create(

                        model=model,

                        file=audio_file

                    )

                    print(f"{response.text}", end="")

                    sys.stdout.flush()

                if os.path.exists(temp_file):

                    os.remove(temp_file)

            except Exception as e:

                print(f"转录失败：{e}")

    finally:

        print("\n转录任务已退出。")

[PRE3]python

import os

import threading

def main():

    """程序入口，初始化并运行录音与转录线程"""

    os.makedirs(TEMP_AUDIO_FOLDER, exist_ok=True)

    audio_queue = queue.Queue()

    stop_event = threading.Event()

    recorder_thread = threading.Thread(

        target=record_audio,

        args=(audio_queue, stop_event)

    )

    transcriber_thread = threading.Thread(

        target=transcribe_audio,

        args=(audio_queue, stop_event)

    )

    recorder_thread.start()

    transcriber_thread.start()

    try:

        while True:

            time.sleep(100)

            pass

    except KeyboardInterrupt:

        print("检测到 Ctrl+C，准备退出...")

        stop_event.set()

        recorder_thread.join()

        transcriber_thread.join()

    finally:

        print("程序结束。")

if __name__ == "__main__":

    main()

[PRE4]python

from dotenv import load_dotenv, find_dotenv

_ = load_dotenv(find_dotenv())

# 选择图片生成厂商
# supplier = "OpenAI"
supplier = "ZhipuAI"

# 按厂商加载对应的客户端与模型
if supplier == "OpenAI":

    from openai import OpenAI

    client = OpenAI()

    model = "dall-e-3"

    functionName = client.images.generate

else:

    from zhipuai import ZhipuAI

    client = ZhipuAI()

    model = "cogview-3-plus"

    functionName = client.images.generations