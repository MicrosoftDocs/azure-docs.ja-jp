---
title: システム要件
description: Azure Remote Rendering のシステム要件を一覧表示します
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.custom: references_regions
ms.openlocfilehash: dd91622344263dc366a76c913ce0be95718550cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101705162"
---
# <a name="system-requirements"></a>システム要件

この章では、*Azure Remote Rendering* (ARR) を操作するための最小システム要件を一覧表示します。

## <a name="development-pc"></a>開発用 PC

* Windows 10 バージョン 1903 以降。
* 最新のグラフィックス ドライバー。
* 省略可能:[H265 ハードウェア ビデオ デコーダー](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)。(たとえば、Unity で) リモートにレンダリングされるコンテンツのローカル プレビューを使用する場合。

> [!IMPORTANT]
> Windows Update では常に最新の GPU ドライバーが提供されるわけではありません。最新のドライバーについては、GPU 製造元の Web サイトを確認してください。
>
> * [AMD ドライバー](https://www.amd.com/en/support)
> * [Intel ドライバー](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA ドライバー](https://www.nvidia.com/Download/index.aspx)

以下の表には、H265 ハードウェア ビデオ デコードをサポートする GPU が一覧表示されています。

| GPU 製造元 | サポートされているモデル |
|-----------|:-----------|
| NVIDIA | [このページの下部にある](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix) **NVDEC サポート マトリックス** を確認してください。 GPU には、**H.265 4:2:0 8 ビット** 列が YES となっているものが必要です。 |
| AMD | 少なくともバージョン 6 の AMD の [Unified Video Decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6) を搭載した GPU。 |
| Intel | Skylake 以降の CPU |

正しい H265 コーデックがインストールされていても、コーデック Dll のセキュリティ プロパティによってコーデックの初期化エラーが発生する可能性があります。 この問題を解決する手順については、[トラブルシューティング ガイド](../resources/troubleshoot.md#h265-codec-not-available)を参照してください。 DLL の問題が発生するのは、たとえば、Unity で、デスクトップ アプリケーションのサービスを使用している場合のみです。

## <a name="devices"></a>デバイス

現在、Azure Remote Rendering では、ターゲット デバイスとして **HoloLens 2** と Windows デスクトップのみがサポートされています。 「[プラットフォームの制限事項](../reference/limits.md#platform-limitations)」セクションを参照してください。

新しいバージョンでは待機時間が大幅に改善されているため、最新の HEVC コーデックを使用することが重要です。 デバイスにインストールされているバージョンを確認するには、次のようにします。

1. **Microsoft Store** を起動します。
1. 右上の **[...]** ボタンをクリックします。
1. **[ダウンロードおよび更新]** を選択します。
1. 一覧で **[デバイス製造元からの HEVC ビデオ拡張機能]** を検索します。 この項目が [更新プログラム] の一覧に表示されていない場合は、最新のバージョンが既にインストールされています。
1. 一覧表示されているコーデックのバージョンが **1.0.21821.0** 以上であることを確認します。
1. **[更新プログラムの入手]** ボタンをクリックし、インストールされるまで待ちます。

## <a name="network"></a>ネットワーク

優れたユーザー エクスペリエンスを実現するには、安定した、低待機時間のネットワーク接続が不可欠です。

[ネットワーク要件](../reference/network-requirements.md)については、専用の章を参照してください。

ネットワーク問題のトラブルシューティングについては、[トラブルシューティング ガイド](../resources/troubleshoot.md#unstable-holograms)を参照してください。

### <a name="network-firewall"></a>ネットワーク ファイアウォール

### <a name="sdk-version--0176"></a>SDK バージョン >= 0.1.76

Remote Rendering 仮想マシンでは、次の IP 範囲からの共有 IP アドレスを使用します。

| 名前             | リージョン         | IP プレフィックス         |
|------------------|:---------------|:------------------|
| オーストラリア東部   | australiaeast  | 20.53.44.240/28   |
| 米国東部          | eastus         | 20.62.129.224/28  |
| 米国東部 2        | eastus2        | 20.49.103.240/28  |
| 東日本       | japaneast      | 20.191.165.112/28 |
| 北ヨーロッパ     | northeurope    | 52.146.133.64/28  |
| 米国中南部 | southcentralus | 20.65.132.80/28   |
| 東南アジア   | southeastasia  | 20.195.64.224/28  |
| 英国南部         | uksouth        | 51.143.209.144/28 |
| 西ヨーロッパ      | westeurope     | 20.61.99.112/28   |
| 米国西部 2        | westus2        | 20.51.9.64/28     |

(デバイス上やルーター内などの) ファイアウォールでこれらの IP 範囲と次のポート範囲がブロックされないようにします。

| Port              | Protocol  | Allow    |
|-------------------|---------- |----------|
| 49152-65534       | TCP / UDP | 送信 |

#### <a name="sdk-version--0176"></a>SDK バージョン < 0.1.76

(デバイス上やルーター内などの) ファイアウォールが次のポートをブロックしていないことを確認します。

| Port              | Protocol | Allow    | 説明 |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | 送信 | 初期接続 (HTTP ハンドシェイク) |
| 8266              | UDP      | 送信 | データ転送 |
| 5000、5433、8443  | TCP      | 送信 | [ArrInspector ツール](../resources/tools/arr-inspector.md)に必要です|


## <a name="software"></a>ソフトウェア

次のソフトウェアがインストールされている必要があります。

* 最新バージョンの **Visual Studio 2019** [(ダウンロード)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio tools for Mixed Reality](/windows/mixed-reality/install-the-tools)。 特に、次の *ワークロード* のインストールは必須です。
  * **C++ によるデスクトップ開発**
  * **ユニバーサル Windows プラットフォーム (UWP) の開発**
* **Windows SDK 10.0.18362.0** [(ダウンロード)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(ダウンロード)](https://git-scm.com/downloads)
* 省略可能:デスクトップ PC のサーバーからビデオ ストリームを表示するには、**HEVC ビデオ拡張機能** [(Microsoft Store リンク)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7) が必要です。 ストアの更新プログラムを確認して、最新バージョンがインストールされていることを確認します。

## <a name="unity"></a>Unity

Unity を使用する開発では、以下のものをインストールします。

* Unity 2019.3.1 [(ダウンロード)](https://unity3d.com/get-unity/download)
* 次のモジュールを Unity にインストールします。
  * **UWP** - ユニバーサル Windows プラットフォーム Build Support
  * **IL2CPP** - Windows Build Support (IL2CPP)

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Unity によるモデルのレンダリング](../quickstarts/render-model.md)