---
title: Azure Kinect Sensor SDK のダウンロード
description: Windows および Linux で Azure Kinect Sensor SDK をダウンロードしてインストールする方法について説明します。
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, 更新プログラムのダウンロード, 最新, 利用可能, インストール
ms.openlocfilehash: 48cdd35a80d68a7ec0d900639c0fca3a2c66787e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171790"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Azure Kinect Sensor SDK のダウンロード

このページには、Azure Kinect Sensor SDK の各バージョンのダウンロード リンクがあります。 このインストーラーには、Azure Kinect 用に開発するために必要なすべてのファイルが用意されています。

## <a name="azure-kinect-sensor-sdk-contents"></a>Azure Kinect Sensor SDK のコンテンツ

- Azure Kinect DK を使用してアプリケーションを構築するためのヘッダーとライブラリ。
- Azure Kinect DK を使用するアプリケーションで必要とされる再頒布可能な DLL。
- [Azure Kinect ビューアー](azure-kinect-viewer.md)。
- [Azure Kinect レコーダー](azure-kinect-recorder.md)。
- [Azure Kinect ファームウェア ツール](azure-kinect-firmware-tool.md)。

## <a name="windows-download-link"></a>Windows のダウンロード リンク

[Microsoft インストーラ](https://download.microsoft.com/download/3/d/6/3d6d9e99-a251-4cf3-8c6a-8e108e960b4b/Azure%20Kinect%20SDK%201.4.1.exe) | [GitHub ソース コード](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

> [!NOTE]
> SDK をインストールするとき、インストール先のパスを覚えておいてください。 たとえば、"C:\Program Files\Azure Kinect SDK 1.2" です。 このパスの記事で参照されているツールが見つかります。

以前のバージョンの Azure Kinect Sensor SDK とファームウェアは [GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md) にあります。

## <a name="linux-installation-instructions"></a>Linux のインストール手順

現在、サポートされているディストリビューションは Ubuntu 18.04 のみです。 その他のディストリビューションのサポートをリクエストするには、[このページ](https://aka.ms/azurekinectfeedback)を参照してください。

最初に、[こちら](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software)の手順に従って、[Microsoft のパッケージ リポジトリ](https://packages.microsoft.com/)を構成する必要があります。

必要なパッケージをインストールできるようになりました。 `k4a-tools` パッケージには、[Azure Kinect ビューアー](azure-kinect-viewer.md)、[Azure Kinect レコーダー](record-sensor-streams-file.md)、[Azure Kinect ファームウェア ツール](azure-kinect-firmware-tool.md)が含まれています。 インストールするには、以下を実行します。

 `sudo apt install k4a-tools`

 `libk4a<major>.<minor>-dev` パッケージには、`libk4a` に対して構築するヘッダーと CMake ファイルが含まれています。
`libk4a<major>.<minor>` パッケージには、`libk4a` に依存する実行可能ファイルを実行するために必要な共有オブジェクトが含まれています。

 基本的なチュートリアルでは、`libk4a<major>.<minor>-dev` パッケージが必要です。 インストールするには、以下を実行します。

 `sudo apt install libk4a1.1-dev`

コマンドが成功すると、SDK を使用できるようになります。

## <a name="change-log-and-older-versions"></a>変更ログと以前のバージョン

Azure Kinect Sensor SDK の変更ログについては、[こちら](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md)をご覧ください。

以前のバージョンの Azure Kinect Sensor SDK が必要な場合は、[こちら](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)をご確認ください。

## <a name="next-steps"></a>次のステップ

[Azure Kinect DK を設定する](set-up-azure-kinect-dk.md)
