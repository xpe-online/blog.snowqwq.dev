# 浅记wf-recorder的用法

> wf-recorder — simple screen recording program for wlroots-based compositors
>
> 内容参考[wf-recorder的manpage](https://man.archlinux.org/man/wf-recorder.1.en)

想着用`wf-recorder`再写个脚本可以不用打开`OBS Studio`快速录制

## 基本用法

```sh 
wf-recorder [agrv]
```

使用`^C`停止录制，默认将会在当前目录下输出`recording.mkv`

## 命令行参数

- -a, --audio `[=DEVICE]`

    启用音频录制，DEVICE参数可选，默认情况下可能会是麦克风（输入设备）

    如果想要录制桌面音频可以用：

    ```sh
    pactl list sources | grep Name
    ```

    找到自己的耳机（输出设备），替换掉`-a=value`里value对应的值即可

    例如`wf-recorder -a=alsa_output.pci-0000_00_1f.3.analog-stereo.monitor`

    写成脚本就更好了

- -c, --codec `<output_codec>`

    指定编解码器，想要看有哪些编解码器可以用：
    
    ```sh
    ffmpeg -hide_banner -encoders | grep -E '^ V' | grep -F '(codec' | cut -c 8- | sort
    ```

    找到自己能用的编码器填进去即可，例如`-c h264_amf`

    - -p --codec-param `[option_name=option_value]`

        指定编解码器参数，参考 **FFmpeg** ？不太会用编解码器参数

        大概常用的有`preset` `crt`

        引用一篇博客：[wklchris/blog](https://wklchris.github.io/blog/FFmpeg/Codecs.html)

- -r, --framerate `<framerate>`

    > Sets hard constant framerate. Will duplicate frames to reach it. This makes the resulting video CFR. Solves FPS limit issue of some encoders. 

    通过复制帧达到指定的固定帧率

- -D, --no-damage

    > By default, wf-recorder will request a new frame from the compositor only when the screen updates. This results in a much smaller output file, which however has a variable refresh rate. When this option is on, wf-recorder does not use this optimization and continuously records new frames, even if there are no updates on the screen. 

    用来稳定刷新率，即使屏幕没有更新也会持续录制新帧

- -f, --file `<filename.ext>`

    指定输出文件名，扩展名决定文件格式

- -g, --geometry `<x,y WxH>`

    截取区域录制，搭配`slurp`使用更佳，例如`-g "$(slurp)"`

- -C --audio-codec `<output_audio_codec>`

    指定音频解码器

    - -P --audio-codecc-param `[option_name=option_value]`

        指定音频解码器参数

- -R --samplle-rate `<saample_rate>`

    指定音频采样率，默认为48000，单位Hz

- -y --overwrite 

    强制覆盖文件而不发出提示

    如果存在同名的文件默认会提示是否覆盖

## 小记

```bash
#!/bin/bash

wf-recorder -a=alsa_output.pci-0000_00_1f.3.analog-stereo.monitor \
    -f "/home/snow/workstation/Videos/wf-recorder/$(date +%Y-%m-%d_%H-%M-%S).mkv" \
    -r 60
```
