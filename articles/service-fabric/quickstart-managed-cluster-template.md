---
title: Azure Resource Manager を使用して Service Fabric マネージド クラスター (プレビュー) をデプロイする
description: Azure Resource Manager テンプレートを使用して Service Fabric マネージド クラスターを作成する方法について説明します
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410421"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>クイックスタート: Azure Resource Manager テンプレートを使用して Service Fabric マネージド クラスター (プレビュー) をデプロイする

Service Fabric マネージド クラスターは、デプロイとクラスター管理エクスペリエンスを効率化する Azure Service Fabric クラスター リソース モデルの進化版です。 Service Fabric マネージド クラスターは、単一の Service Fabric クラスター リソースをデプロイできる完全にカプセル化されたリソースで、Service Fabric クラスターを構成する基盤リソースをすべてデプロイする必要がありません。 この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure でのテスト用に Service Fabric マネージド クラスターをデプロイする方法について説明します。

このチュートリアルでデプロイする 3 ノードの Basic SKU クラスターは、説明のみを目的としたものであり、運用環境のワークロードは意図していません。 詳細については、「[Service Fabric マネージド クラスターの SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus)」を参照してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートを開始する前に:

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用するテンプレートは、[Azure サンプルの Service Fabric クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT)から入手したものです。

## <a name="create-a-client-certificate"></a>クライアント証明書を作成する

Service Fabric マネージド クラスターでは、アクセスの制御用のキーとしてクライアント証明書が使用されます。 クラスターへのアクセスの制御に使用するクライアント証明書が既にある場合は、この手順はスキップしてかまいません。

新しいクライアント証明書を作成する必要がある場合は、[Azure Key Vault から証明書の設定と取得を行う方法](../key-vault/certificates/quick-create-portal.md)に関するページの手順に従ってください。

証明書の拇印を書き留めておいてください。これは次の手順でテンプレートをデプロイする際に必要となります。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。

      [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. 次の値を選択または入力します

    このクイックスタートでは、以下のテンプレート パラメーターに独自の値を指定します。

    * **サブスクリプション**:Azure サブスクリプションを選択します。
    * **リソース グループ**: **[新規作成]** を選択します。 リソース グループの一意の名前 (*myResourceGroup* など) を入力し、 **[OK]** を選択します。
    * **[場所]** :場所 (**eastus2** など) を選択します。 Service Fabric マネージド クラスター プレビューは、`centraluseuap`、`eastus2euap`、`eastasia`、`northeurope`、`westcentralus`、`eastus2` などのリージョンでサポートされます。
    * **クラスター名**:自分のクラスター用に一意の名前 (*mysfcluster* など) を入力します。
    * **管理ユーザー名**: クラスター内の基になる VM で RDP に使用する管理者の名前を入力します。
    * **管理パスワード**: クラスター内の基になる VM で RDP に使用する管理者のパスワードを入力します。
    * **クライアント証明書の拇印**:クラスターにアクセスするために使用するクライアント証明書の拇印を指定します。 証明書がない場合は、[証明書の設定と取得](../key-vault/certificates/quick-create-portal.md)に関する手順に従って、自己署名証明書を作成します。
    * **ノード タイプ名**: ノード タイプの一意の名前を入力します (例: *nt1*)。
    * **[上記の使用条件に同意する]** : このボックスをオンにして同意します。 

3. **[購入]** を選択します。

4. マネージド Service Fabric クラスターがデプロイされるまでには数分かかります。 デプロイが正常に完了するまで待ってから次の手順に移動してください。

## <a name="validate-the-deployment"></a>デプロイの検証

### <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

デプロイが完了したら、出力の中の Service Fabric Explorer 値を見つけ、Web ブラウザーでそのアドレスを開いて、Service Fabric Explorer でクラスターを表示します。 証明書の入力を求められたら、テンプレートでクライアントの拇印を指定した証明書を使用します。

> [!NOTE]
> デプロイの出力は、Azure portal の [resource group deployments]\(リソース グループのデプロイ\) タブで確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Service Fabric マネージド クラスターのリソース グループは、不要になったら削除してください。 ポータルを使用してリソース グループを削除するには:

1. ポータルの上部にある *検索* ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、マネージド Service Fabric クラスターをデプロイしました。 クラスターをスケーリングする方法の詳細については、次のページを参照してください。

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターをスケールアウトする](tutorial-managed-cluster-scale.md)
