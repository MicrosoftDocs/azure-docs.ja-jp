---
title: Microsoft Azure Stack Edge Pro のシステム要件 | Microsoft Docs
description: Azure Stack Edge Pro のソフトウェア要件とネットワーキング要件について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: c87c8cfd40903f6296f0a36f5c83c0b5c5cb7818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831581"
---
# <a name="azure-stack-edge-pro-system-requirements"></a>Azure Stack Edge Pro のシステム要件

この記事では、Microsoft Azure Stack Edge Pro ソリューション、および Azure Stack Edge Pro に接続するクライアントのシステム要件のうち、重要なものについて説明します。 この情報を慎重に確認してから Azure Stack Edge Pro をデプロイすることをお勧めします。 展開中およびその後の操作中に、必要に応じてこの情報を参照できます。

Azure Stack Edge Pro のシステム要件は次のとおりです。 

- **ホストのソフトウェア要件** - サポートされているプラットフォーム、ローカル構成 UI 用のブラウザー、SMB クライアント、およびデバイスにアクセスするクライアントのその他の要件について説明します。
- **デバイスのネットワーク要件** - 物理デバイスの操作のためのネットワーク要件について説明します。

## <a name="supported-os-for-clients-connected-to-device"></a>デバイスに接続されるクライアントでサポートされている OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>デバイスにアクセスするクライアントでサポートされるプロトコル

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>サポートされるストレージ アカウント

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>サポートされているストレージの種類

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>ローカル Web UI 用にサポートされているブラウザー

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>ネットワーク ポートの要件

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Azure Stack Edge Pro のポート要件

SMB、クラウド、または管理トラフィックを許可するためにファイアウォールで開く必要があるポートを次の表に示します。 この表では、"*イン*" ("*受信*") は、着信クライアント要求がデバイスにアクセスする方向を意味します。 "*アウト*" ("*送信*") は Azure Stack Edge Pro デバイスがデプロイを超えて外部に (たとえば、インターネットに) データを送信する方向を意味します。

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge ポートの要件

Azure IoT Edge では、サポートされている IoT Hub プロトコルを使用した、オンプレミスの Edge デバイスから Azure クラウドへのアウトバウンド通信が許可されています。 インバウンド通信が必要なのは、Azure IoT Hub がメッセージを Azure IoT Edge デバイスにプッシュ ダウンする必要がある特定のシナリオのみです (たとえば、クラウドからデバイスへのメッセージング)。

Azure IoT Edge ランタイムをホストするサーバーのポート構成には、次の表を使用します。

| ポート番号 | インまたはアウト | ポート範囲 | 必須 | ガイダンス |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| アウト       | WAN        | はい      | IoT Edge のプロビジョニングのため、送信用に開きます。 この構成は、手動スクリプトや Azure IoT Device Provisioning Service (DPS) を使用する場合に必要です。|

詳細は、[IoT Edge デプロイのファイアウォール規則とポート構成規則](../iot-edge/troubleshoot.md)を参照してください。

## <a name="url-patterns-for-firewall-rules"></a>ファイアウォール ルールの URL パターン

多くの場合、ネットワーク管理者は、受信トラフィックと送信トラフィックをフィルターする URL パターンに基づいて、高度なファイアウォール ルールを構成できます。 Azure Stack Edge Pro デバイスとサービスは、Azure Service Bus、Azure Active Directory Access Control、ストレージ アカウント、Microsoft Update サーバーなど、他の Microsoft アプリケーションに依存しています。 その Microsoft アプリケーションと関連付けられた URL パターンを使用してファイアウォール ルールを構成できます。 Microsoft アプリケーションに関連付けられた URL パターンは変化する可能性がある点を理解することが重要です。 この変更のため、ネットワーク管理者は必要に応じて Azure Stack Edge Pro のファイアウォール ルールを監視し更新する必要があります。

ほとんどの場合、送信トラフィックのファイアウォール ルールは Azure Stack Edge Pro 固定 IP アドレスに基づいて設定することが推奨されます。 ただし、次の情報を使用して、セキュリティで保護された環境を作成するのにために必要な高度なファイアウォール ルールを設定することもできます。

> [!NOTE]
> - デバイスの (送信元) IP は、常にすべてのクラウド対応ネットワーク インターフェイスに合わせて設定します。
> - 宛先 IP は、[Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に合わせて設定するようにします。

### <a name="url-patterns-for-gateway-feature"></a>ゲートウェイ機能の URL パターン

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>コンピューティング機能の URL パターン

| URL パターン                      | コンポーネントまたは機能                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft コンテナー レジストリ (必須)               |
| https://\*.azurecr.io                     | 個人やサード パーティのコンテナー レジストリ (任意) | 
| https://\*.azure-devices.net              | IoT Hub アクセス (必須)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Azure Government 用のゲートウェイの URL パターン

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure Government 用のコンピューティングの URL パターン

| URL パターン                      | コンポーネントまたは機能                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft コンテナー レジストリ (必須)               |
| https://\*.azure-devices.us              | IoT Hub アクセス (必須)           |
| https://\*.azurecr.us                    | 個人やサード パーティのコンテナー レジストリ (任意) | 

## <a name="internet-bandwidth"></a>インターネット帯域幅

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>コンピューティングのサイズに関する考慮事項

ソリューションの開発とテスト中は、ご自身の経験を活用して、Azure Stack Edge Pro デバイスに十分な容量があること、およびデバイスから最適なパフォーマンスが得られることを確認します。

考慮すべき要素には、以下が含まれます。

- **コンテナーの詳細** - 以下について検討します。

    - ワークロード内のコンテナーはいくつあるか。 多数の軽量のコンテナーまたは少数のリソース集約型のコンテナーがある可能性があります。
    - これらのコンテナーに割り当てられるリソースは何か、これらのコンテナーが消費するリソースは何か。
    - コンテナーで共有されるレイヤーはいくつあるか。
    - 未使用のコンテナーはあるか。 停止されたコンテナーも、ディスク領域を消費します。
    - コンテナーはどの言語で記述されるか。
- **処理されるデータのサイズ** - コンテナーで処理されるデータの量はどれくらいか。 このデータはディスク領域を消費するのか、メモリで処理されるのか。
- **期待されるパフォーマンス** - ソリューションの望ましいパフォーマンス特性は何か。 

ソリューションのパフォーマンスを理解して改良するために、以下を使用できます。

- Azure portal で入手できるコンピューティング メトリック。 Azure Stack Edge リソースに移動し、 **[監視] > [メトリック]** に移動します。 **[Edge コンピューティング - メモリ使用量]** と **[Edge コンピューティング - CPU の割合]** を調べて、使用できるリソースとリソースがどのように消費されているかを理解します。
- デバイスの PowerShell インターフェイスから次のような監視コマンドを使用できます。

    - `dkr`: コンテナー リソースの使用状況統計のライブ ストリームを取得します。 このコマンドは、CPU、メモリ使用量、メモリの制限、およびネットワーク IO のメトリックをサポートします。
    - `dkr system df`: 使用されたディスク領域の量に関する情報を取得します。 
    - `dkr image [prune]`: 未使用のイメージをクリーンアップして領域を解放します。
    - `dkr ps --size`: 実行中のコンテナーのおおよそのサイズを表示します。 

    使用可能なコマンドの詳細については、「[Monitor and troubleshoot compute modules (コンピューティング モジュールの監視とトラブルシューティング)](azure-stack-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)」を参照してください。

最後に、お使いのデータセットに対するご自身のソリューションの検証を実行し、運用環境にデプロイする前に Azure Stack Edge Pro でパフォーマンスを数量化します。


## <a name="next-step"></a>次のステップ

- [Azure Stack Edge Pro をデプロイする](azure-stack-edge-deploy-prep.md)