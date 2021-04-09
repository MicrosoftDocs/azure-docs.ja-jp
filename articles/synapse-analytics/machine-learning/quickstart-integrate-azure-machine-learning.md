---
title: クイック スタート:Azure Machine Learning ワークスペースをリンクする
description: Synapse ワークスペースを Azure Machine Learning ワークスペースにリンクする
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 800cbf9b7a7fa415452f29b253347188c8917c52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219455"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>クイック スタート:Synapse で Azure Machine Learning のリンクされたサービスを新規作成する

このクイックスタートでは、Azure Synapse Analytics ワークスペースを Azure Machine Learning ワークスペースにリンクします。 これらのワークスペースをリンクすると、Synapse のさまざまなエクスペリエンスから Azure Machine Learning を利用できるようになります。

たとえば、Azure Machine Learning ワークスペースにリンクすると、次のようなエクスペリエンスが可能になります。

- Azure Machine Learning パイプラインを Synapse パイプラインのステップとして実行する。 詳細については、[Azure Machine Learning パイプラインの実行](../../data-factory/transform-data-machine-learning-service.md)に関する記事を参照してください。

- Azure Machine Learning モデル レジストリから機械学習モデルをコピーすることで、予測によってデータを強化し、Synapse SQL プールでモデルにスコアを付けます。 詳細については、「[チュートリアル: Synapse SQL プール向けの機械学習モデル スコアリング ウィザード](tutorial-sql-pool-model-scoring-wizard.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)。
- ADLS Gen2 ストレージ アカウントが既定のストレージとして構成されている [Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 使用する ADLS Gen2 ファイル システムの **ストレージ BLOB データ共同作成者** である必要があります。
- [Azure Machine Learning ワークスペース](../../machine-learning/how-to-manage-workspace.md)。
- リンクされたサービスの作成に使用できるサービス プリンシパルとシークレットを作成するには、アクセス許可 (またはアクセス許可を持つユーザーからの要求) が必要です。 このサービス プリンシパルには Azure Machine Learning ワークスペースの共同作成者ロールが割り当てられている必要があることに注意してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com/)

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

この手順では、新しいサービス プリンシパルを作成します。 既存のサービス プリンシパルを使用する場合は、次の手順をスキップできます。
1. Azure portal を開きます。 

1. **[Azure Active Directory]**  ->  **[アプリの登録]** に移動します。

1. **[新規登録]** をクリックします。 次に、UI の指示に従って、新しいアプリケーションを登録します。

1. アプリケーションが登録された後、 アプリケーションのシークレットを生成します。 **[アプリケーション]**  ->  **[証明書およびシークレット]** の順に移動します。 **[クライアント シークレットを追加]** をクリックして、シークレットを生成します。 シークレットは安全に保管してください。これは後で使用します。

   ![シークレットの生成](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. アプリケーション用にサービス プリンシパルを作成します。 **[アプリケーション]**  ->  **[該当]** の順に移動し、 **[サービス プリンシパルの作成]** をクリックします。 場合によっては、このサービス プリンシパルは自動的に作成されます。

   ![サービス プリンシパルの作成](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. サービス プリンシパルを Azure Machine Learning ワークスペースの "共同作成者" として追加します。 これは、Azure Machine Learning ワークスペースが属しているリソース グループの所有者である必要があることに注意してください。

   ![共同作成者ロールの割り当て](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

## <a name="create-a-linked-service"></a>リンクされたサービスを作成する

1. 新しい Azure Machine Learning のリンクされたサービスを作成する Synapse ワークスペースで、 **[管理]**  ->  **[リンクされたサービス]** に移動し、タイプを "Azure Machine Learning" として新しいリンクされたサービスを作成します。

   ![リンクされたサービスの作成](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. 以下をフォームに入力します。

   - サービス プリンシパル ID: これはアプリケーションの **アプリケーション (クライアント) ID** です。
  
     > [!NOTE]
     > アプリケーションの名前ではありません。 この ID は、アプリケーションの ID の概要ページで確認できます。 これは、"81707eac-ab38-406u-8f6c-10ce76a568d5" のような長い文字列になります。

   - サービス プリンシパルのキー: 前のセクションで生成したシークレットです。

3. **[接続のテスト]** をクリックして、構成が正しいかどうかを確認します。 接続テストが成功した場合は、 **[保存]** をクリックします。

   接続テストが失敗した場合は、サービス プリンシパル ID とシークレットが正しいことを確認してから、もう一度やり直してください。

## <a name="next-steps"></a>次の手順

- [チュートリアル: 機械学習モデル スコアリング ウィザード - 専用 SQL プール](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics の機械学習機能](what-is-machine-learning.md)