---
title: Android で Speech SDK を使用してコーデック圧縮オーディオをストリーミングする - Speech Service
titleSuffix: Azure Cognitive Services
description: Android で Speech SDK を使用して圧縮オーディオを Azure Speech Services にストリーミングする方法について学習します。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: df5eb123a2fd47a3eceea8153786442bf56a2718
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803800"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>Android で Speech SDK を使用してコーデック圧縮オーディオ入力を使用する

Speech SDK の**圧縮オーディオ入力ストリーム** API では、PullStream または PushStream を使用して、圧縮オーディオを Speech サービスにストリーミングできます。

> [!IMPORTANT]
> 現在、圧縮入力オーディオのストリーミングは、[Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9) では C++、C#、Java](how-to-use-codec-compressed-audio-input-streams.md) でサポートされています。 また、Android では Java、[iOS プラットフォームでは Objective-C](how-to-use-codec-compressed-audio-input-streams-ios.md) でもサポートされています。
> Speech SDK バージョン 1.7.0 以降が必要です。

wav/PCM については、メインライン音声のドキュメントを参照してください。  wav/PCM 以外では、次のコーデック圧縮入力形式がサポートされています。

- MP3
- OPUS/OGG
- FLAC
- wav コンテナー内の ALAW
- wav コンテナー内の MULAW

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>Android でコーデック圧縮オーディオ入力を使用するための前提条件

コーデック圧縮オーディオは、[GStreamer](https://gstreamer.freedesktop.org) を使用して実装されます。 ライセンスの理由から、SDK では Gstreamer バイナリはコンパイルされません。 Android では、事前構築済みのバイナリを使用する必要があります。 事前構築済みのライブラリをダウンロードするには、「[Android 開発用のインストール](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c)」を参照してください。 

`libgstreamer_android.so` は必須です。 GStreamer プラグインが `libgstreamer_android.so` でリンクされていることを確認してください。

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

例の `Android.mk` と `Application.mk` ファイルを次に示します。 この手順に従って、gstreamer 共有オブジェクト (`libgstreamer_android.so`) を作成します。

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

Ubuntu 16.04 または 18.04 で次のコマンドを使用して、`libgstreamer_android.so` を構築できます。 次のコマンド ラインは、[Android NDK b16b](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip) での [Gstreamer Android バージョン 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) に対してのみテストされています。

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

共有オブジェクト (libgstreamer_android.so) が構築されると、アプリケーション開発者は、Speech SDK で読み込むことができるように、共有オブジェクトを Android アプリに配置する必要があります。

## <a name="example-code-using-codec-compressed-audio-input"></a>コーデック圧縮オーディオ入力を使用するコード例

圧縮オーディオ形式で Speech サービスにストリーミングするには、`PullAudioInputStream` または `PushAudioInputStream` を作成します。 次に、ストリームの圧縮形式を指定して、ストリーム クラスのインスタンスから `AudioConfig` を作成します。

`myPullStream` という入力ストリーム クラスがあり、OPUS/OGG を使用しているものと想定します。 コードは次のようになります。

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>次の手順

- [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [C# で音声を認識する方法を確認する](quickstart-csharp-dotnet-windows.md)
