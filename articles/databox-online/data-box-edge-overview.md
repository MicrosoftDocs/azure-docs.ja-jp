---
title: Microsoft Azure Data Box Edge の概要 | Microsoft Docs
description: Azure へのネットワーク ベースの転送に物理デバイスを使用するストレージ ソリューションである Azure Data Box Edge について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305433"
---
# <a name="what-is-azure-data-box-edge"></a>Azure Data Box Edge とは 

Azure Data Box Edge は、ネットワーク データ転送の機能を備えた AI 対応のエッジ コンピューティング デバイスです。 この記事では、Data Box Edge ソリューションの概要、利点、主な機能、このデバイスを配置できるシナリオについて説明します。 

Data Box Edge は、サービスとしてのハードウェア ソリューションです。 Microsoft は、高速な AI 推論を実現し、ストレージ ゲートウェイのすべての機能を備える、Field Programmable Gate Array (FPGA) が組み込まれたクラウドマネージド デバイスを提供しています。 

## <a name="use-cases"></a>ユース ケース

ここでは、Data Box Edge を使用してエッジで高速な機械学習 (ML) 推論を実行し、データを Azure に送信する前に前処理するさまざまなシナリオを示します。

- **Inference Azure Machine Learning** - Data Box Edge により、ML モデルを実行して、データがクラウドに送信される前に対応できるように、迅速な結果を得ることができます。 必要に応じて、ML モデルを再調整して改善し続けるために、データ セット全体を転送することができます。 Data Box Edge デバイスで Azure ML ハードウェア アクセラレータ モデルを使用する方法の詳細については、[Data Box Edge への Azure ML ハードウェア アクセラレーション対応モデルのデプロイ](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)に関するページを参照してください。

- **データの前処理** - データを Azure に送信する前に変換して、より実用的なデータセットを作成します。 前処理は以下の目的で使用できます。 

    - データを集計します。
    - 個人データを削除するなど、データを変更します。
    - ストレージと帯域幅を最適化したり、詳細な分析を行ったりするためのサブセット データ。
    - 分析して、IoT イベントに対応します。 

- **ネットワーク経由でデータを Azure に転送する** - 計算や分析をさらに可能にするか、アーカイブ目的のために、Data Box Edge を使用して、Azure にデータを簡単かつ迅速に転送します。 


## <a name="key-capabilities"></a>主な機能

Data Box Edge には次の機能があります。

|機能 |説明  |
|---------|---------|
|高速 AI 推論| 組み込みの FPGA によって有効にされます。|
|コンピューティング       |データの分析、処理、フィルター処理が可能です。|
|高性能 | ハイ パフォーマンス コンピューティングおよびデータ転送。|
|データ アクセス     | クラウドでデータをさらに処理するために、クラウド API を使用して、Azure Storage Blob と Azure ファイルからデータを直接アクセスします。 デバイス上のローカル キャッシュは、最近使用されたファイルの高速アクセスに使用されます。|
|クラウドでの管理     |デバイスとサービスは Azure portal を介して管理されます。  |
|オフライン アップロード     | 切断モードでは、オフライン アップロードのシナリオに対応します。|
|サポートされるプロトコル     | データ インジェストのために、標準の SMB プロトコルと NFS プロトコルをサポートします。 <br> サポート対象のバージョンについては、「[Data Box Edge のシステム要件](data-box-edge-system-requirements.md)」を参照してください。|
|データ更新     | ローカル ファイルをクラウドから最新の情報に更新する機能。|
|暗号化    | ローカルでデータを暗号化し、クラウドへのデータ転送を *https* 経由で保護する BitLocker のサポート。|
|帯域幅調整| ピーク時の帯域幅の使用量を制限するように調整します。|


## <a name="components"></a>コンポーネント

Data Box Edge ソリューションは、Data Box Edge リソース、Data Box Edge 物理デバイス、ローカル Web UI で構成されています。

* **Data Box Edge 物理デバイス** - Azure にデータを送信するように構成可能な Microsoft 製の 1U ラックマウント型サーバー。 
    
* **Data Box リソース** - さまざまな地理的な場所からアクセスできる Web インターフェイスから、Data Box デバイスを管理できる、Azure portal でのリソース。 Data Box Edge リソースを使用して、リソースの作成と管理、デバイスとアラートの表示と管理、および共有の管理を行います。  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    詳細については、[Data Box Edge デバイスの注文の作成](data-box-edge-deploy-prep.md#create-a-new-resource)に関するページを参照してください。

* **Data Box ローカル Web UI** - ローカル Web UI を使用して、診断の実行、Data Box Edge デバイスのシャットダウンと再起動し、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の提出を行います。

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Web ベース UI の使用については、「[Web ベース UI を使用した Data Box の管理](data-box-edge-manage-access-power-connectivity-mode.md)」を参照してください。


## <a name="region-availability"></a>利用可能なリージョン

Data Box Edge 物理デバイス、Azure リソース、データ転送先のターゲット ストレージ アカウントは、すべて同じ地域にある必要はありません。

- **リソースの使用可能性** - Data Box Edge リソースを使用できるすべてのリージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)」をご覧ください。 Data Box Edge は、Azure Government クラウドにデプロイすることもできます。 詳細については、「[What is Azure Government? (Azure Government とは)](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)」を参照してください。
    
- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、すべての Azure 地域で利用できます。 パフォーマンスを最適にするには、ストレージ アカウントによって Data Box Edge のデータが格納されるリージョンと、デバイスが配置されている場所を、近くにする必要があります。 デバイスから離れた場所にあるストレージ アカウントは、待ち時間が長くなり、パフォーマンスが低下します。 


## <a name="next-steps"></a>次の手順

- [Data Box Edge のシステム要件](data-box-edge-system-requirements.md) を確認する。
- [Data Box Edge の制限事項](data-box-edge-limits.md) を理解する。
- Azure portal で [Azure Data Box Edge](data-box-edge-deploy-prep.md) を配置する。




