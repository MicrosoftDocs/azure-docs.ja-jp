---
title: クイック スタート:Java (Windows、Linux、macOS) 用 Speech SDK のプラットフォームの設定 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech サービス SDK を使用して Java (Windows、Linux、macOS) 用にプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: 2a6516ad5f0fc8d9faefd7b7f89ddb1eaa3fd7d6
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375302"
---
このガイドでは、64 ビット Java 8 JRE 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。 このパッケージ名の使用を自分で開始する場合、Java SDK は Maven Central Repository で使用できません。 Gradle または `pom.xml` 依存関係ファイルを使用しているかどうかに関係なく、`https://csspeechstorage.blob.core.windows.net/maven/` を指すカスタム リポジトリを追加する必要があります (パッケージ名については以下を参照してください)。

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md) を参照してください。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

- 以下のオペレーティング システム用の Java Speech SDK パッケージを入手できます。
  - Windows: 64 ビットのみ
  - Mac: macOS X バージョン 10.13 以降
  - Linux: Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 7 または 8、CentOS 7 または 8 の 64 ビットのみ

## <a name="prerequisites"></a>前提条件

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) または [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (Java が既にインストールされている必要があります)
- サポートされている Linux プラットフォームでは、特定のライブラリがインストールされている必要があります (Secure Sockets Layer サポート用に `libssl`、サウンド サポート用に `libasound2`)。 これらのライブラリの正しいバージョンをインストールするために必要なコマンドについては、下記のディストリビューションを参照してください。

  - Ubuntu では、以下のコマンドを実行して、必要なパッケージをインストールします。

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  - Debian 9 では、以下のコマンドを実行して、必要なパッケージをインストールします。

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

  - RHEL または CentOS では、以下のコマンドを実行して、必要なパッケージをインストールします。

    ```sh
    sudo yum update
    sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
    ```

> [!NOTE]
> - RHEL または CentOS 7 の場合、「[Speech SDK 用に RHEL/CentOS 7 を構成する](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)」の手順に従います。
> - RHEL または CentOS 8 の場合、「[Linux 用 OpenSSL の構成](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)」の手順に従います。

- Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)が必要です。 パッケージを初めてインストールする場合、このガイドを続行する前に Windows の再起動が必要になる場合があることに注意してください。

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Eclipse プロジェクトを作成して Speech SDK をインストールする

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>次のステップ

[!INCLUDE [windows](../quickstart-list.md)]
