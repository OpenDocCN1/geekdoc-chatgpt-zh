实时语音转写 并发实操

思路很直接：把收音和识别拆开，各自跑在一路并行任务里。收音一路不断从麦克风抓取采样，按 5 秒一组打包成 wav 落到缓存目录，再把文件地址推进任务通道。识别一路则一直监听通道，有新文件就用 Whisper-1 调用转写，拿到文本立刻输出，处理完顺手清理文件。主入口负责准备缓存目录、创建两路任务并用事件标志协同，遇到用户主动中断时设置终止标志，等待两路都收尾后退出。

另外还放了一个模型切换的微型示例，用 supplier 这个开关在 OpenAI 的 DALL·E 3 和智谱的 CogView-3-Plus 之间切换，展示如何按平台初始化客户端和调用函数名。

[PRE0]python

import pyaudio

import wave

# 配置参数

CHUNK = 1024  # 每次读取的音频数据块大小(字节)

FORMAT = pyaudio.paInt16  # 音频采样格式,16位整型

CHANNELS = 1  # 音频通道数,单声道

RATE = 16000  # 采样率

RECORD_SECONDS = 5  # 每段录制时长

TEMP_AUDIO_FOLDER = "temp_audio"  # 临时音频文件夹

[PRE1]python

def record_audio(audio_queue, stop_event):

    """录制音频并将文件名添加到队列中"""

    p = pyaudio.PyAudio()

    stream = p.open(format=FORMAT,

                    channels=CHANNELS,

                    rate=RATE,

                    input=True,

                    frames_per_buffer=CHUNK)

    file_index = 0

    print("正在录音……")

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

        print("\n 录音线程已停止。")

[PRE2]python

import queue

import time

import sys

from dotenv import load_dotenv, find_dotenv

from openai import OpenAI

def transcribe_audio(audio_queue, stop_event):

    """从队列中获取音频文件并进行转录"""

    _ = load_dotenv(find_dotenv())

    client = OpenAI()

    model = "whisper-1"

    print("开始转录......")

    try:

        while not stop_event.is_set():

            try:

                temp_file = audio_queue.get(timeout=1)

            except queue.Empty:

                continue

            if temp_file is None:

                continue

            try:

                # print(f"正在转录音频文件:{temp_file}")

                with open(temp_file, "rb") as audio_file:

                    response = client.audio.transcriptions.create(

                        model=model,

                        file=audio_file

                    )

                    print(f"{response.text}", end="")

                    sys.stdout.flush()

                # 删除临时文件

                if os.path.exists(temp_file):

                    os.remove(temp_file)

            except Exception as e:

                print(f"转录错误:{e}")

    finally:

        print("\n转录线程已停止。")

[PRE3]python

import os

import threading

def main():

    """主函数:启动录音和转录线程"""

    # 创建临时文件夹

    os.makedirs(TEMP_AUDIO_FOLDER, exist_ok=True)

    # 启动录音和转录线程

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

    # 设置键盘中断

    try:

        while True:

            time.sleep(100)

            pass

    except KeyboardInterrupt:

        print("捕获到键盘中断,正在停止程序……")

        stop_event.set()

        recorder_thread.join()

        transcriber_thread.join()

    finally:

        print("程序已退出。")

if __name__ == "__main__":

    main()

[PRE4]python

from dotenv import load_dotenv, find_dotenv

_ = load_dotenv(find_dotenv())

# 确定选择使用的模型服务平台

# supplier = "OpenAI"

supplier = "ZhipuAI"

# 根据服务平台初始化模型名称及 API

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