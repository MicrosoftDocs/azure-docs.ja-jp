---
title: Visual Studio (SSDT) を使用して Azure Analysis Services にデプロイする | Microsoft Docs
description: SSDT を使用して Azure Analysis Services サーバーに表形式モデルをデプロイする方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b9740d74a25964286ea92b4238684db81a64c9e2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793321"
---
# <a name="deploy-a-model-from-visual-studio"></a>Visual Studio からモデルをデプロイする

Azure サブスクリプションにサーバーを作成した後は、表形式モデル データベースをサーバーにデプロイできます。 表形式モデル プロジェクトの作成とデプロイは、SQL Server Data Tools (SSDT) を使って行うことができます。 

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure の **Analysis Services サーバー**。 詳しくは、「[Azure Analysis Services サーバーを作成する](analysis-services-create-server.md)」をご覧ください。
* SSDT の**表形式モデル プロジェクト**または 1200 以上の互換性レベルの既存の表形式モデル。 作成していない場合は、 [Adventure Works Internet Sales の表形式モデルのチュートリアル](/sql/analysis-services/tabular-modeling-adventure-works-tutorial)を参照して作成してください。
* **オンプレミス ゲートウェイ** - 組織のネットワークにオンプレミスのデータ ソースがある場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をインストールする必要があります。 ゲートウェイは、クラウドのサーバーがオンプレミスのデータ ソースに接続してモデルのデータを処理および更新するために必要です。

> [!TIP]
> デプロイする前に、テーブルのデータを処理できることを確認します。 SSDT で、**[モデル]** > **[処理]** > **[すべて処理]** の順にクリックします。 処理に失敗した場合、正常にデプロイできません。
> 
> 

## <a name="get-the-server-name"></a>サーバー名の取得

**Azure Portal** でサーバーを選び、**[概要]** > **[サーバー名]** のサーバー名をコピーします。
   
![Azure でサーバー名を取得する](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>SSDT からデプロイするには

1. SSDT の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[プロパティ]** を選びます。 **[配置]**  >  **[サーバー]** にサーバー名を貼り付けます。   
   
    ![配置サーバー プロパティにサーバー名を貼り付ける](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. **ソリューション エクスプローラー**で、**[プロパティ]** を右クリックし、**[配置]** をクリックします。 Azure へのサインインを要求される場合があります。
   
    ![サーバーにデプロイする](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    デプロイの状態が、[出力] ウィンドウと [デプロイ] の両方に表示されます。
   
    ![[デプロイ ステータス]](./media/analysis-services-deploy/aas-deploy-status.png)

これですべて完了です。


## <a name="troubleshooting"></a>トラブルシューティング

メタデータをデプロイするとデプロイが失敗する場合、おそらく SSDT がサーバーに接続できないことが原因です。 SSMS を使ってサーバーに接続できることを確認してください。 その後、プロジェクトの [配置サーバー] プロパティが正しいことを確認します。

テーブルでデプロイが失敗する場合は、サーバーがデータ ソースに接続できない可能性があります。 オンプレミスのデータ ソースが組織のネットワークにある場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をインストールする必要があります。

## <a name="next-steps"></a>次の手順

サーバーに表形式モデルをデプロイしたので、モデルに接続できます。 [SSMS で接続](analysis-services-manage.md)して管理できます。 また、Power BI、Power BI Desktop、Excel などの[クライアント ツールを使って接続](analysis-services-connect.md)し、レポートの作成を始めることができます。

