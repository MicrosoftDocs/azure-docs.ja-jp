---
title: "Azure Analysis Services にデプロイする | Microsoft Docs"
description: "Azure Analysis Services サーバーに表形式モデルをデプロイする方法について説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 18d64f1ad4ef0dd41ae9302d08e02e94d1c608f5
ms.lasthandoff: 11/17/2016


---
# <a name="deploy-to-azure-analysis-services"></a>Azure Analysis Services にデプロイする
Azure サブスクリプションにサーバーを作成した後は、表形式モデル データベースをサーバーにデプロイできます。 表形式モデル プロジェクトの作成とデプロイは、SQL Server Data Tools (SSDT) を使って行うことができます。 または、SQL Server Management Studio (SSMS) を使って、Analysis Services インスタンスから既存の表形式モデル データベースをデプロイすることもできます。

## <a name="before-you-begin"></a>開始する前に
開始するには、以下が必要です。

* Azure の **Analysis Services サーバー**。 詳しくは、[Azure での Analysis Services の作成に関する記事](analysis-services-create-server.md)をご覧ください。
* Analysis Services インスタンス上の互換性レベル 1200 の SSDT の**表形式モデル プロジェクト**または既存の表形式モデル。 作成していない場合は、 [Adventure Works チュートリアル](https://msdn.microsoft.com/library/hh231691.aspx)をご覧ください。
* **オンプレミス ゲートウェイ** - 組織のネットワークにオンプレミスのデータ ソースがある場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をインストールする必要があります。 ゲートウェイは、クラウドのサーバーがオンプレミスのデータ ソースに接続してモデルのデータを処理および更新するために必要です。

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>SSDT から表形式モデルをデプロイするには
SSDT からデプロイする場合は、2016 年 9 月 30 日以降に更新された[最新バージョン](https://msdn.microsoft.com/library/mt204009.aspx)を使っていることを確認します。

> [!TIP]
> デプロイする前に、テーブルのデータを処理できることを確認します。 SSDT で、**[モデル]** > **[処理]** > **[すべて処理]** の順にクリックします。 処理が失敗する場合、デプロイも失敗します。
> 
> 

1. デプロイする前に、サーバー名を取得する必要があります。 **Azure Portal** でサーバーを選び、**[概要]** > **[サーバー名]** のサーバー名をコピーします。
   
    ![Azure でサーバー名を取得する](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. SSDT の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[プロパティ]** を選びます。 **[配置]**  >  **[サーバー]** にサーバー名を貼り付けます。   
   
    ![配置サーバー プロパティにサーバー名を貼り付ける](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. **ソリューション エクスプローラー**で、**[プロパティ]** を右クリックし、**[配置]** をクリックします。 Azure へのサインインを要求される場合があります。
   
    ![サーバーにデプロイする](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    デプロイの状態が、[出力] ウィンドウと [デプロイ] の両方に表示されます。
   
    ![[デプロイ ステータス]](./media/analysis-services-deploy/aas-deploy-status.png)

これですべて完了です。

## <a name="to-deploy-using-xmla-script"></a>XMLA スクリプトを使ってデプロイするには
1. SSMS でデプロイする表形式モデル データベースを右クリックし、**[スクリプト]** > **[データベースをスクリプト化]** > **[CREATE]** の順にクリックして、場所を選びます。
2. デプロイ先のサーバー インスタンスで、クエリを実行します。 同じサーバーにデプロイする場合、XMLA スクリプトの少なくとも **name** プロパティを変更する必要があります。  

## <a name="but-something-went-wrong"></a>問題が発生する場合
メタデータをデプロイするとデプロイが失敗する場合、おそらく SSDT がサーバーに接続できないことが原因です。 SSMS を使ってサーバーに接続できることを確認してください。 その後、プロジェクトの [配置サーバー] プロパティが正しいことを確認します。

テーブルでデプロイが失敗する場合は、サーバーがデータ ソースに接続できない可能性があります。 オンプレミスのデータ ソースが組織のネットワークにある場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をインストールする必要があります。

## <a name="next-steps"></a>次のステップ
サーバーに表形式モデルをデプロイしたので、モデルに接続できます。 [SSMS で接続](analysis-services-manage.md)して管理できます。 また、Power BI、Power BI Desktop、Excel などの[クライアント ツールを使って接続](analysis-services-connect.md)し、レポートの作成を始めることができます。


