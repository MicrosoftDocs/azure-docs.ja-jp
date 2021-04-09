---
title: Azure Kinect Sensor SDK のダウンロード
description: Windows および Linux で Azure Kinect Sensor SDK をダウンロードしてインストールする方法について説明します。
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, 更新プログラムのダウンロード, 最新, 利用可能, インストール
ms.openlocfilehash: 591fcba4c887e298cf667c5d95c19184bc213ffe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179631"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Azure Kinect Sensor SDK のダウンロード

このページには、Azure Kinect Sensor SDK の各バージョンのダウンロード リンクがあります。 このインストーラーには、Azure Kinect 用に開発するために必要なすべてのファイルが用意されています。

## <a name="azure-kinect-sensor-sdk-contents"></a>Azure Kinect Sensor SDK のコンテンツ

- Azure Kinect DK を使用してアプリケーションを構築するためのヘッダーとライブラリ。
- Azure Kinect DK を使用するアプリケーションで必要とされる再頒布可能な DLL。
- [Azure Kinect ビューアー](azure-kinect-viewer.md)。
- [Azure Kinect レコーダー](azure-kinect-recorder.md)。
- [Azure Kinect ファームウェア ツール](azure-kinect-firmware-tool.md)。

## <a name="windows-installation-instructions"></a>Windows のインストール手順

Azure Kinect Sensor SDK とファームウェアの最新バージョンと以前のバージョンのインストールの詳細については、[こちら](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)を参照してください。

ソース コードについては、[こちら](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)を参照してください。

> [!NOTE]
> SDK をインストールするとき、インストール先のパスを覚えておいてください。 たとえば、"C:\Program Files\Azure Kinect SDK 1.2" です。 このパスの記事で参照されているツールが見つかります。

## <a name="linux-installation-instructions"></a>Linux のインストール手順

現在、サポートされているディストリビューションは Ubuntu 18.04 のみです。 その他のディストリビューションのサポートをリクエストするには、[このページ](https://aka.ms/azurekinectfeedback)を参照してください。

最初に、[こちら](/windows-server/administration/linux-package-repository-for-microsoft-software)の手順に従って、[Microsoft のパッケージ リポジトリ](https://packages.microsoft.com/)を構成する必要があります。

必要なパッケージをインストールできるようになりました。 `k4a-tools` パッケージには、[Azure Kinect ビューアー](azure-kinect-viewer.md)、[Azure Kinect レコーダー](record-sensor-streams-file.md)、[Azure Kinect ファームウェア ツール](azure-kinect-firmware-tool.md)が含まれています。 パッケージをインストールするには、以下を実行します。

`sudo apt install k4a-tools`
 
このコマンドでは、ツールが正常に動作するために必要な依存関係パッケージがインストールされます。これには、`libk4a<major>.<minor>` の最新バージョンも含まれます。 root ユーザーにならずに Azure Kinect DK にアクセスするには、udev ルールを追加する必要があります。 手順については、「[Linux デバイスのセットアップ](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md#linux-device-setup)」を参照してください。 別の方法として、デバイスを root として使用するアプリケーションを起動することもできます。

`libk4a<major>.<minor>-dev` パッケージには、`libk4a` に対してアプリケーション/実行可能ファイルを構築するためのヘッダーと CMake ファイルが含まれています。

`libk4a<major>.<minor>` パッケージには、`libk4a` に依存するアプリケーション/実行可能ファイルを実行するために必要な共有オブジェクトが含まれています。

基本的なチュートリアルでは、`libk4a<major>.<minor>-dev` パッケージが必要です。 パッケージをインストールするには、以下を実行します。

`sudo apt install libk4a<major>.<minor>-dev` 

コマンドが成功すると、SDK を使用できるようになります。

必ず、`libk4a<major>.<minor>-dev` と一致するバージョンの `libk4a<major>.<minor>` をインストールしてください。 たとえば、`libk4a4.1-dev` パッケージをインストールする場合は、一致するバージョンの共有オブジェクト ファイルが含まれる、対応する `libk4a4.1` パッケージをインストールします。 `libk4a` の最新バージョンについては、次のセクションのリンクを参照してください。

## <a name="change-log-and-older-versions"></a>変更ログと以前のバージョン

Azure Kinect Sensor SDK の変更ログについては、[こちら](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md)をご覧ください。

以前のバージョンの Azure Kinect Sensor SDK が必要な場合は、[こちら](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)をご確認ください。

## <a name="next-steps"></a>次のステップ

[Azure Kinect DK を設定する](set-up-azure-kinect-dk.md)
