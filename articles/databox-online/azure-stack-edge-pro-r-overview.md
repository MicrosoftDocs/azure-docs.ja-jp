---
title: Microsoft Azure Stack Edge Pro R の概要 | Microsoft Docs
description: Azure へのネットワーク ベースの転送に物理デバイスを使用する、軍事用アプリケーション向けのストレージ ソリューションである Azure Stack Edge Pro R デバイスについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2ff07995cab2da6e53600be9bf60b30d1896cc9d
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "102637079"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>Azure Stack Edge Pro R とは

Azure Stack Edge Pro R は、過酷な環境で使用するために設計された頑丈なエッジ コンピューティング デバイスです。 Azure Stack Edge Pro R は、サービスとしてのハードウェア ソリューションとして提供されます。 ネットワーク ストレージ ゲートウェイとして機能し、かつ、高速な AI 推論を実現する GPU (グラフィカル処理ユニット) 搭載のクラウドマネージド デバイスをお届けします。

この記事では、Azure Stack Edge Pro R ソリューションの概要、主要な機能、このデバイスを配置できるシナリオについて説明します。


## <a name="key-capabilities"></a>主な機能

Azure Stack Edge Pro R には、次の機能があります。

|機能 |説明  |
|---------|---------|
|ラグド ハードウェア| 過酷な環境向けに設計された、ラグドなサーバー クラス ハードウェア。 携帯性に優れた輸送用ケースに収まるデバイス。 |
|クラウドでの管理     |デバイスとサービスは Azure portal を介して管理されます。|
|Edge コンピューティング ワークロード   |データの分析、処理、フィルター処理が可能です。 VM とコンテナー化されたワークロードがサポートされています。|
|高速 AI 推論| Nvidia T4 GPU によって実現されます。|
|データ アクセス     | クラウドでデータをさらに処理するために、クラウド API を使用して、Azure Storage Blob と Azure ファイルからデータを直接アクセスします。 デバイス上のローカル キャッシュは、最近使用されたファイルの高速アクセスに使用されます。|
|切断モード| デバイスとサービスは、必要に応じて Azure Stack Hub を介して管理できます。 アプリケーションをオフライン モードで展開、実行、管理します。 <br> 切断モードでは、オフライン アップロードのシナリオに対応します。|
|サポートされているファイル転送プロトコル     |データ インジェストのために、標準の SMB、NFS、REST プロトコルをサポートします。 <br> サポート対象のバージョンについては、[Azure Stack Edge Pro R のシステム必要条件](azure-stack-edge-gpu-system-requirements.md)に関する記事をご覧ください。|
|データ更新     | ローカル ファイルをクラウドから最新の情報に更新する機能。|
|二重暗号化    | 自己暗号化ドライブの使用によって暗号化の最初のレイヤーが提供されます。 VPN によって暗号化の第 2 レイヤーが提供されます。 ローカルでデータを暗号化し、クラウドへのデータ転送を *https* 経由で保護する BitLocker のサポート。|
|帯域幅調整| ピーク時の帯域幅の使用量を制限するように調整します。|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>ユース ケース

ここでは、Azure Stack Edge Pro R を使用してエッジで高速な機械学習 (ML) 推論を実行し、データを Azure に送信する前に前処理するさまざまなシナリオを示します。

- **Azure Machine Learning による推論** - Azure Stack Edge Pro R により、ML モデルを実行して、データがクラウドに送信される前に対応できるように、迅速な結果を得ることができます。 必要に応じて、ML モデルを再調整して改善し続けるために、データ セット全体を転送することができます。 Azure Stack Edge Pro R デバイスで Azure ML ハードウェア アクセラレータ モデルを使用する方法の詳細については、[Azure Stack Edge Pro R への Azure ML ハードウェア アクセラレーション対応モデルのデプロイ](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)に関する記事を参照してください。

- **データの前処理** - データを Azure に送信する前に変換して、より実用的なデータセットを作成します。 前処理は以下の目的で使用できます。

    - データを集計します。
    - 個人データを削除するなど、データを変更します。
    - ストレージと帯域幅を最適化したり、詳細な分析を行ったりするためのサブセット データ。
    - 分析して、IoT イベントに対応します。

- **ネットワーク経由でデータを Azure に転送する** - Azure Stack Edge Pro R を使用して簡単かつ迅速に Azure にデータを転送し、さらなるコンピューティングや分析を行ったり、アーカイブしたりできます。

## <a name="components"></a>コンポーネント

Azure Stack Edge Pro R ソリューションは、Azure Stack Edge リソース、Azure Stack Edge Pro R のラグドな物理デバイス、およびローカル Web UI で構成されています。

- **Azure Stack Edge Pro R 物理デバイス** - 頑丈な輸送用ケースに収まる、1 ノードのコンピューティングおよびストレージ デバイス。 オプションで無停電電源装置 (UPS) もご利用いただけます。

    ![Azure Stack Edge Pro R の 1 ノード デバイス](media/azure-stack-edge-pro-r-overview/device-image-1.png)

- **Azure Stack Edge リソース** - さまざまな地理的な場所からアクセス可能な Web インターフェイスから、ラグドな Azure Stack Edge Pro R デバイスを管理できる、Azure portal でのリソース。 Azure Stack Edge リソースを使用して、リソースの作成と管理、デバイスとアラートの表示と管理、および共有の管理を行います。  

- **Azure Stack Edge Pro R のローカル Web UI** - Azure Stack Edge Pro R デバイス上のブラウザー ベースのローカル ユーザー インターフェイス。主にデバイスの初期構成を想定しています。 他にも、ローカル Web UI を使用して、診断の実行、Azure Stack Edge Pro デバイスのシャットダウンと再起動し、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の提出を行います。


## <a name="region-availability"></a>利用可能なリージョン

Azure Stack Edge Pro R 物理デバイス、Azure リソース、データ転送先のターゲット ストレージ アカウントは、すべて同じリージョン内にある必要はありません。

- **リソースの使用可能性** - Azure Stack Edge リソースを使用できるすべてのリージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)」を参照してください。 

- **デバイスの可用性** - Azure Stack Edge Pro R デバイスが使用可能なすべての国の一覧については、[Azure Stack Edge Pro R の価格](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR)に関するページの **[Azure Stack Edge Pro R]** タブにある **[可用性]** セクションにアクセスしてください。

- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、すべての Azure 地域で利用できます。 最適なパフォーマンスを得るには、ストレージ アカウントによって Azure Stack Edge Pro R のデータが格納されるリージョンと、デバイスが配置される場所を近くにする必要があります。 デバイスから離れた場所にあるストレージ アカウントは、待ち時間が長くなり、パフォーマンスが低下します。

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro R のシステム必要条件](azure-stack-edge-gpu-system-requirements.md)を確認します。
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->