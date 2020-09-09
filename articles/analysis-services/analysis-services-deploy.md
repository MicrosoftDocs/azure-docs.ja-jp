---
title: Visual Studio を使用して Azure Analysis Services にモデルをデプロイする | Microsoft Docs
description: Visual Studio を使用して Azure Analysis Services サーバーに表形式モデルをデプロイする方法について説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d6b737985bc19aa09b26bb0d4a65696a364a903a
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697596"
---
# <a name="deploy-a-model-from-visual-studio"></a>Visual Studio からモデルをデプロイする

Azure サブスクリプションにサーバーを作成した後は、表形式モデル データベースをサーバーにデプロイできます。 Analysis Services プロジェクトで Visual Studio を使用すると、作業中の表形式モデル プロジェクトを構築してデプロイできます。 

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure の **Analysis Services サーバー**。 詳しくは、「[Azure Analysis Services サーバーを作成する](analysis-services-create-server.md)」をご覧ください。
* Visual Studio の**表形式モデル プロジェクト**または 1200 以上の互換性レベルの既存の表形式モデル。 作成していない場合は、 [Adventure Works Internet Sales の表形式モデルのチュートリアル](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial)を参照して作成してください。
* **オンプレミス ゲートウェイ** - 組織のネットワークにオンプレミスのデータ ソースがある場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をインストールする必要があります。 ゲートウェイは、クラウドのサーバーがオンプレミスのデータ ソースに接続してモデルのデータを処理および更新するために必要です。

> [!TIP]
> デプロイする前に、テーブルのデータを処理できることを確認します。 Visual Studio で、 **[モデル]**  >  **[処理]**  >  **[すべて処理]** の順にクリックします。 処理に失敗した場合、正常にデプロイできません。
> 
> 

## <a name="get-the-server-name"></a>サーバー名の取得

**Azure Portal** でサーバーを選び、 **[概要]**  >  **[サーバー名]** のサーバー名をコピーします。
   
![Azure でサーバー名を取得する](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Visual Studio からデプロイするには

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックして **[プロパティ]** を選択します。 **[配置]**  >  **[サーバー]** にサーバー名を貼り付けます。   
   
    ![配置サーバー プロパティにサーバー名を貼り付ける](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. **ソリューション エクスプローラー**で、 **[プロパティ]** を右クリックし、 **[配置]** をクリックします。 Azure へのサインインを要求される場合があります。
   
    ![サーバーにデプロイする](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    デプロイの状態が、[出力] ウィンドウと [デプロイ] の両方に表示されます。
   
    ![[デプロイ ステータス]](./media/analysis-services-deploy/aas-deploy-status.png)

これですべて完了です。


## <a name="troubleshooting"></a>トラブルシューティング

メタデータのデプロイ時にデプロイが失敗する場合は、Visual Studio がサーバーに接続できなかったためである可能性があります。 SQL Server Management Studio (SSMS) を使用してサーバーに接続できることを確認します。 その後、プロジェクトの [配置サーバー] プロパティが正しいことを確認します。

テーブルでデプロイが失敗する場合は、サーバーがデータ ソースに接続できない可能性があります。 オンプレミスのデータ ソースが組織のネットワークにある場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をインストールする必要があります。

## <a name="next-steps"></a>次のステップ

サーバーに表形式モデルをデプロイしたので、モデルに接続できます。 [SQL Server Management Studio (SSMS) を使用して接続](analysis-services-manage.md)し、そのモデルを管理できます。 また、Power BI、Power BI Desktop、Excel などの[クライアント ツールを使って接続](analysis-services-connect.md)し、レポートの作成を始めることができます。   

高度なデプロイ方法については、「[表形式モデル ソリューションのデプロイ](https://docs.microsoft.com/analysis-services/deployment/tabular-model-solution-deployment?view=azure-analysis-services-current)」を参照してください。



