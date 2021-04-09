---
title: ネットワークの分離
description: ユーザーは QnA Maker リソースへのパブリック アクセスを制限できます。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: afb396bc364a2fa2db923fbcbe6bfe1b7aedbc26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467546"
---
# <a name="recommended-settings-for-network-isolation"></a>ネットワーク分離の推奨設定

QnA Maker リソースへのパブリック アクセスを制限するには、次の手順に従う必要があります。 [仮想ネットワークを構成](../../cognitive-services-virtual-networks.md?tabs=portal)して、Cognitive Services リソースをパブリック アクセスから保護します。

## <a name="restrict-access-to-app-service-qna-runtime"></a>App Service (QnA Runtime) へのアクセスを制限する

App Service 許可リストに IP を追加してアクセスを制限したり、QnA Maker App Service をホストするように App Service Environment を構成したりできます。

#### <a name="add-ips-to-app-service-allow-list"></a>App Service 許可リストに IP を追加する

1. Cognitive Services IP からのトラフィックのみを許可します。 これらは、既にサービスタグ `CognitiveServicesManagement` に含まれています。 これは、オーサリング API シリーズ (KB の作成/更新) で App Service を起動し、それに応じて Azure Search Service を更新するために必要です。 [サービス タグに関する詳細](../../../virtual-network/service-tags-overview.md)を確認します。
2. Azure Bot Service、QnA Maker ポータルなどの他のエントリ ポイントにも予測 "GenerateAnswer" API アクセスを許可していることを確認します。
3. IP アドレスの範囲を許可リストに追加するには、次の手順に従ってください。

   1. [すべてのサービス タグの IP 範囲](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードします。
   2. "CognitiveServicesManagement" の IP を選択します。
   3. App Service リソースのネットワーク セクションに移動し、[アクセス制限の構成] オプションをクリックして許可リストに IP を追加します。

また、App Service に対して同じ処理を行う自動スクリプトも用意されています。 [許可リストを構成するための PowerShell スクリプト](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1)については、GitHub 上にあります。 スクリプト パラメーターとして、サブスクリプション ID、リソース グループ、および実際の App Service 名を入力する必要があります。 スクリプトを実行すると、App Service の許可リストに IP が自動的に追加されます。

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>QnA Maker App Service をホストするように App Service Environment を構成する
    
App Service Environment (ASE) を使用して、QnA Maker App Service をホストできます。 次の手順に従ってください。

1. App Service Environment を作成し、"外部" としてマークします。 手順については、[チュートリアル](../../../app-service/environment/create-external-ase.md)を参照してください。
2.  App Service Environment 内に App Service を作成します。
    1. App Service の構成を確認し、アプリケーション設定として "PrimaryEndpointKey" を追加します。 'PrimaryEndpointKey' の値は、“\<app-name\>-PrimaryEndpointKey” に設定されている必要があります。 アプリ名は、App Service の URL で定義されています。 たとえば、App Service の URL が "mywebsite.myase.p.azurewebsite.net" の場合、アプリ名は "mywebsite" になります。 この場合、'PrimaryEndpointKey' の値は “mywebsite-PrimaryEndpointKey” に設定されている必要があります。
    2. Azure Search サービスを作成します。
    3. Azure Search とアプリの設定が適切に構成されていることを確認します。 
          この[チュートリアル](../reference-app-service.md?tabs=v1#app-service)を参照してください。
3.  App Service Environment に関連付けられているネットワーク セキュリティ グループを更新します。
    1. 事前に作成された受信セキュリティ規則を要件に従って更新します。
    2. ソースが 'Service Tag'、ソース サービス タグが 'CognitiveServicesManagement' の新しい受信セキュリティ規則を追加します。
       
    ![受信ポートの例外](../media/inbound-ports.png)

4.  Azure Resource Manager を使用して QnA Maker Cognitive Services インスタンス (Microsoft.CognitiveServices/accounts) を作成します。QnA Maker エンドポイントは上記で作成した App Service Environment (https:// mywebsite.myase.p.azurewebsite.net) に設定する必要があります。
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>Cognitive Search リソースへのアクセスを制限する

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

Cognitive Search インスタンスは、QnA Maker リソースの作成後、プライベート エンドポイントを介して分離できます。 プライベート エンドポイントで接続するには、VNet 経由で Search Service インスタンスにアクセスできる必要があります。 

QnA Maker App Service が App Service Environment を使用して制限される場合、同じ VNet を使用し、Cognitive Search インスタンスへのプライベート エンドポイント接続を作成できます。 VNet で新しい DNS エントリを作成し、Cognitive Search エンドポイントを Cognitive Search プライベート エンドポイント IP アドレスにマップします。 

QnAMaker App Service に App Service Environment が使用されない場合、まず、新しい VNet リソースを作成してから Cognitive Search インスタンスへのプライベート エンドポイント接続を作成します。 この場合、Cognitive Search インスタンスに接続するには、QnA Maker App Service を [VNet と統合する](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)必要があります。 

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

Azure Search リソースに[プライベート エンドポイントを作成](../reference-private-endpoint.md)します。

---
