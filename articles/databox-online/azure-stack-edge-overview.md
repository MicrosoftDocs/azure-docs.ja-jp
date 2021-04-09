---
title: Microsoft Azure Stack Edge Pro の概要 | Microsoft Docs
description: Azure へのネットワーク ベースの転送に物理デバイスを使用するストレージ ソリューションである Azure Stack Edge Pro について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/15/2021
ms.author: alkohli
ms.openlocfilehash: 3973235991a16d118b47d7289f3a1825621a9023
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574681"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>FPGA 搭載の Azure Stack Edge Pro とは

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

FPGA 搭載の Azure Stack Edge Pro は、ネットワーク データ転送機能を備えた AI 対応のエッジ コンピューティング デバイスです。 この記事では、FPGA 搭載の Azure Stack Edge Pro ソリューションの概要、利点、主な機能、およびデプロイ シナリオについて説明します。

FPGA 搭載の Azure Stack Edge Pro は、サービスとしてのハードウェア ソリューションです。 Microsoft は、高速な AI 推論を実現し、ネットワーク ストレージ ゲートウェイのすべての機能を備える、Field Programmable Gate Array (FPGA) が組み込まれたクラウドマネージド デバイスを提供しています。

Azure Data Box Edge は、Azure Stack Edge にブランドが変更されました。

## <a name="use-cases"></a>ユース ケース

ここでは、Azure Stack Edge Pro を使用してエッジで高速な機械学習 (ML) 推論を実行し、データを Azure に送信する前に前処理するさまざまなシナリオを示します。

- **Azure Machine Learning による推論** - Azure Stack Edge Pro により、ML モデルを実行して、データがクラウドに送信される前に対応できるように、迅速な結果を得ることができます。 必要に応じて、ML モデルを再調整して改善し続けるために、データ セット全体を転送することができます。 Azure Stack Edge Pro デバイスで Azure ML ハードウェア アクセラレータ モデルを使用する方法の詳細については、[Azure Stack Edge Pro への Azure ML ハードウェア アクセラレーション対応モデルのデプロイ](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)に関する記事を参照してください。

- **データの前処理** - データを Azure に送信する前に変換して、より実用的なデータセットを作成します。 前処理は以下の目的で使用できます。 

    - データを集計します。
    - 個人データを削除するなど、データを変更します。
    - ストレージと帯域幅を最適化したり、詳細な分析を行ったりするためのサブセット データ。
    - 分析して、IoT イベントに対応します。 

- **ネットワーク経由でデータを Azure に転送する** - 計算や分析をさらに進めたり、アーカイブ目的のために、Azure Stack Edge Pro を使用して、Azure にデータを簡単かつ迅速に転送します。 

## <a name="key-capabilities"></a>主な機能

Azure Stack Edge Pro には、次の機能があります。

|機能 |説明  |
|---------|---------|
|高速 AI 推論| 組み込みの FPGA によって有効にされます。|
|コンピューティング       |データの分析、処理、フィルター処理が可能です。|
|高パフォーマンス | ハイ パフォーマンス コンピューティングおよびデータ転送。|
|データ アクセス     | クラウドでデータをさらに処理するために、クラウド API を使用して、Azure Storage Blob と Azure ファイルからデータを直接アクセスします。 デバイス上のローカル キャッシュは、最近使用されたファイルの高速アクセスに使用されます。|
|クラウドでの管理     |デバイスとサービスは Azure portal を介して管理されます。  |
|オフライン アップロード     | 切断モードでは、オフライン アップロードのシナリオに対応します。|
|サポートされるプロトコル     | データ インジェストのために、標準の SMB プロトコルと NFS プロトコルをサポートします。 <br> サポート対象のバージョンについては、[Azure Stack Edge Pro のシステム要件](azure-stack-edge-system-requirements.md)に関するページを参照してください。|
|データ更新     | ローカル ファイルをクラウドから最新の情報に更新する機能。|
|暗号化    | ローカルでデータを暗号化し、クラウドへのデータ転送を *https* 経由で保護する BitLocker のサポート。|
|帯域幅調整| ピーク時の帯域幅の使用量を制限するように調整します。|
|ExpressRoute | ExpressRoute によってセキュリティが強化されました。 ローカル デバイスからクラウド ストレージ エンドポイントへのトラフィックが ExpressRoute 経由で転送されるピアリング構成を使用します。 詳細については、「[ExpressRoute の概要](../expressroute/expressroute-introduction.md)」を参照してください。

## <a name="components"></a>コンポーネント

Azure Stack Edge Pro ソリューションは、Azure Stack Edge リソース、Azure Stack Edge Pro 物理デバイス、およびローカル Web UI で構成されています。

* **Azure Stack Edge Pro 物理デバイス**: Azure にデータを送信するように構成できる、Microsoft によって提供される 1U ラックマウント型サーバー。
    
* **Azure Stack Edge リソース**: さまざまな地理的な場所からアクセスできる Web インターフェイスから Azure Stack Edge Pro デバイスを管理できるようにする Azure portal のリソース。 Azure Stack Edge リソースを使用して、リソースの作成と管理、共有の管理、およびデバイスとアラートの表示と管理を行います。
  
   <!--[The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

   Azure Stack Edge Pro はサポート終了に近づいているため、新しい Azure Stack Edge Pro デバイスの注文には応じていません。 新しいお客様は、ワークロードに対して Azure Stack Edge Pro - GPU デバイスの使用を検討することをお勧めします。 詳細については、「[GPU 搭載の Azure Stack Edge Pro とは](azure-stack-edge-gpu-overview.md)」を参照してください。 GPU デバイス搭載の Azure Stack Edge Pro を注文する方法の詳細については、[Azure Stack Edge Pro - GPU 用に新しいリソースを作成する](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)ことに関する記事を参照してください。

   既存のお客様は、既存の Azure Stack Edge Pro デバイスを交換またはリセットする必要がある場合でも、新しい Azure Stack Edge Pro リソースを作成できます。 手順については、[Azure Stack Edge Pro デバイスの注文の作成](azure-stack-edge-deploy-prep.md#create-new-resource-for-existing-device)に関する記事を参照してください。

* **Azure Stack Edge Pro ローカル Web UI** - ローカル Web UI を使用して、Azure Stack Edge Pro デバイスの診断、シャットダウン、および再起動を実行し、コピー ログを表示し、Microsoft サポートに連絡してサービス リクエストを提出します。

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Web ベース UI の使用については、[Web ベース UI を使用した Azure Stack Edge Pro の管理](azure-stack-edge-manage-access-power-connectivity-mode.md)に関する記事を参照してください。

## <a name="region-availability"></a>利用可能なリージョン

Azure Stack Edge Pro 物理デバイス、Azure リソース、およびデータの転送先であるターゲット ストレージ アカウントは、すべてが同じリージョンに存在している必要はありません。

- **リソースの使用可能性** - Azure Stack Edge リソースを使用できるすべてのリージョンの一覧については、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)を参照してください。 Azure Stack Edge Pro は、Azure Government クラウドにデプロイすることもできます。 詳細については、「[What is Azure Government? (Azure Government とは)](../azure-government/documentation-government-welcome.md)」を参照してください。
    
- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、すべての Azure 地域で利用できます。 パフォーマンスを最適にするには、ストレージ アカウントによって Azure Stack Edge Pro のデータが格納されるリージョンと、デバイスが配置されている場所を、近くにする必要があります。 デバイスから離れた場所にあるストレージ アカウントは、待ち時間が長くなり、パフォーマンスが低下します。

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro のシステム要件](azure-stack-edge-system-requirements.md)を確認します。
- [Azure Stack Edge Pro の制限](azure-stack-edge-limits.md)を理解します。
- Azure portal で [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md) を配置します。