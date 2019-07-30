---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
description: Azure Kubernetes (AKS) リソースを作成する方法について説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1d68c08f6dfca74c38973af1686d614f3f10cc28
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455097"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>Azure Kubernetes Service (AKS) クラスター リソースの作成

1. Kubernetes サービスの [[作成]](https://ms.portal.azure.com/#create/microsoft.aks) に移動します。

1. **[基本]** タブで、以下の詳細を入力します。

    |Setting|値|
    |--|--|
    |サブスクリプション|適切なサブスクリプションを選択します|
    |リソース グループ|使用可能なリソース グループを選択します|
    |Kubernetes クラスター名|希望する名前 (小文字)|
    |リージョン|近くの場所を選択します|
    |Kubernetes バージョン|1.12.8 (既定値)|
    |DNS 名プレフィックス|自動的に作成されますが、必要に応じて上書きできます|
    |ノード サイズ|Standard DS2 v2:<br>`2 vCPUs`、`7 GB`|
    |ノード数|スライダーを既定値のままにします|

1. **[スケール]** タブで、 *[virtual nodes]\(仮想ノード\)* および *[virtual machine scale sets]\(仮想マシン スケール セット\)* は既定値のままにします。
1. **[認証]** タブで、 *[サービス プリンシパル]* および *[Enable RBAC]\(RBAC を有効にする\)* は既定値のままにします。
1. **[ネットワーク]** タブで、次の選択を入力します。

    |Setting|値|
    |--|--|
    |HTTP アプリケーション ルーティング|いいえ|
    |ネットワーク構成|Basic|

1. **[Monitoring]\(監視\)** タブで、 *[Enable container monitoring]\(コンテナーの監視を有効にする\)* が **[はい]** に設定されていることを確認し、 *[Log Analytics workspace]\(Log Analytics ワークスペース\)* は既定値のままにします
1. **[タグ]** タブで、名前と値のペアは今は空のままにしておきます。
1. **[確認と作成]** をクリックします。
1. 検証に合格した後、 **[作成]** をクリックします。

> [!NOTE]
> 検証に失敗した場合、*サービス プリンシパル*のエラーが原因である可能性があります。 *[認証]* タブに戻り、 *[Review + create]\(確認と作成\)* に戻って、検証を実行して合格する必要があります。
