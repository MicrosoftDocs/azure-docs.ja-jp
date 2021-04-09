---
title: Azure portal で Azure Arc データ コントローラーを作成する
description: Azure portal で Azure Arc データ コントローラーを作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 9c928040aa2ff5a6ebfb7102c03450d3d7297b59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686480"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Azure portal で Azure Arc データ コントローラーを作成する

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>はじめに

Azure portal を使用して Azure Arc データ コントローラーを作成できます。

Azure Arc の作成エクスペリエンスの多くは、作成または管理されるリソースが Azure インフラストラクチャの外部にある場合でも、Azure portal で開始されます。 このような場合のユーザー エクスペリエンス パターンでは、特に Azure とご使用の環境の間に直接的な接続がない場合は、Azure portal を使用してスクリプトを生成します。このスクリプトは、Azure へのセキュリティで保護された接続を確立するために、環境内でダウンロードして実行できます。 たとえば、Azure Arc 対応サーバーでは、このパターンに従って [Arc 対応サーバーを作成します](../servers/onboard-portal.md)。

現時点では、プレビューで Azure Arc 対応データ サービスの間接接続モードのみがサポートされているため、ダウンロードして、Kubernetes クラスターに対して Azure Data Studio で実行できるノートブックを生成するために、Azure portal を使用できます。 今後、直接接続モードを使用できるようになると、Azure portal からデータ コントローラーを直接プロビジョニングできます。 [接続モード](connectivity.md)の詳細については、こちらを参照してください。

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Azure portal を使用して Azure Arc データ コントローラーを作成する

Azure portal と Azure Data Studio を使用して Azure Arc データ コントローラーを作成するには、次の手順に従います。

1. 最初に、[Azure portal マーケットプレース](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller)にログインします。  "Azure Arc データ コントローラー" が表示されるように、マーケットプレースの検索結果がフィルター処理されます。
2. 最初のステップで検索条件が入力されなかった場合は、 検索結果で "Azure Arc データ コントローラー" をクリックしてください。
3. マーケットプレースから [Azure データ コントローラー] タイルを選択します。
4. **[作成]** ボタンをクリックします。
5. Azure Arc データ コントローラーを作成するための要件を確認し、Azure Data Studio や kubectl など、不足している前提条件ソフトウェアがあれば、それらをインストールします。
6. **[データ コントローラーの詳細]** ボタンをクリックします。
7. Azure portal で作成する他のリソースと同じように、サブスクリプション、リソース グループ、Azure の場所を選択します。 この場合、Azure の場所には、リソースに関するメタデータが格納される場所を選択します。  リソース自体は、選択するインフラストラクチャ上に作成されます。 必ずしも Azure インフラストラクチャを使用する必要はありません。
8. データ コレクターの名前を入力します。
9. データ コントローラーの接続モードを選択します。 [接続モードと要件](./connectivity.md)の詳細について確認してください。 

   > [!NOTE] 
   > **直接** 接続モードを選択した場合、「[サービス プリンシパルの作成](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)」の説明に従って、サービス プリンシパルの資格情報が環境変数を使用して設定されていることを確認してください。 

1. デプロイ構成ファイルを選択します。
1. **[Azure Studio で開く]** ボタンをクリックします。
1. 次の画面には、選択内容の概要と、生成されたノートブックが表示されます。  **[プロビジョニングしているノートブックのダウンロード]** ボタンをクリックして、ノートブックをダウンロードできます。

   > [!IMPORTANT]
   > Azure Red Hat OpenShift または Red Hat OpenShift コンテナー プラットフォームでは、データ コントローラーを作成する前にセキュリティ コンテキスト制約を適用する必要があります。 「[OpenShift で Azure Arc 対応データ サービスのセキュリティ コンテキスト制約を適用する](how-to-apply-security-context-constraint.md)」の手順に従います。

1. Azure Data Studio でノートブックを開き、上部にある **[すべて実行]** ボタンをクリックします。
1. 表示されるプロンプトとノートブックの指示に従って、データ コントローラーの作成を完了します。

## <a name="monitoring-the-creation-status"></a>作成状態の監視

コントローラーの作成が完了するまでに数分かかります。 次のコマンドを使用して、別のターミナル ウィンドウで進行状況を監視できます。

> [!NOTE]
>  次のコマンド例では、"arc" という名前のデータ コントローラーと Kubernetes 名前空間が作成されていることを前提としています。  異なる名前の名前空間/データ コントローラーを使用している場合は、"arc" をその名前で置き換えることができます。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

次のようなコマンドを実行して、特定のポッドの作成状態を確認することもできます。  これは特に、何らの問題をトラブルシューティングするのに役立ちます。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>作成の問題のトラブルシューティング

作成で問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。