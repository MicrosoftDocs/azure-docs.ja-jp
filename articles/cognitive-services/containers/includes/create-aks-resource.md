---
title: Azure Kubernetes Service クラスター リソースの作成
titleSuffix: Azure Cognitive Services
description: Azure Kubernetes Service (AKS) リソースを作成する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e7f5b6f3685a94b5497784360f8f12b22fb95012
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017870"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Azure Kubernetes Service クラスター リソースの作成

1. [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks) に移動し、 **[作成]** を選択します。

1. **[基本]** タブで、次の情報を入力します。

    |設定|値|
    |--|--|
    |サブスクリプション|適切なサブスクリプションを選択します。|
    |Resource group|使用可能なリソース グループを選択します。|
    |Kubernetes クラスター名|名前 (小文字) を入力します。|
    |リージョン|近くの場所を選択します。|
    |Kubernetes バージョン|**(既定)** とマークされている任意の値。|
    |DNS 名プレフィックス|自動的に作成されますが、オーバーライドできます。|
    |ノード サイズ|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |ノード数|スライダーを既定値のままにします。|

1. **[ノード プール]** タブで、 **[仮想ノード]** および **[VM Scale Sets]** は既定値のままにします。
1. **[認証]** タブで、 **[サービス プリンシパル]** と **[RBAC を有効にします]** は既定値のままに設定します。
1. **[ネットワーク]** タブで、次の選択を入力します。

    |設定|値|
    |--|--|
    |HTTP アプリケーション ルーティング|いいえ|
    |ネットワーク構成|Basic|

1. **[統合]** タブで、 **[コンテナー監視]** が **[有効]** に設定されていることを確認し、 **[Log Analytics ワークスペース]** は既定値のままにします。
1. **[タグ]** タブで、名前と値のペアは今は空のままにしておきます。
1. **[確認と作成]** を選択します。
1. 検証に合格した後、 **[作成]** を選択します。

> [!NOTE]
> 検証に失敗した場合、"サービス プリンシパル" のエラーが原因である可能性があります。 **[認証]** タブに戻り、 **[Review + create]\(確認と作成\)** に戻って、検証を実行して合格する必要があります。
