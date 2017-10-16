---
title: "Azure Analysis Services チュートリアル - レッスン 13: デプロイする | Microsoft Docs"
description: "Azure Analysis Services にチュートリアル用プロジェクトをデプロイする方法を説明します｡"
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/20/2017
ms.author: owend
ms.openlocfilehash: 9594582419c716a37a1010e2410e7b2b199df04c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-13-deploy"></a>レッスン 13: デプロイする

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

このレッスンでは、デプロイのプロパティを構成します。そのためにまず、デプロイ先となる Azure Analysis Services サーバーとモデルの名前を指定し、 そのインスタンスにモデルをデプロイします｡ モデルをデプロイすると､ユーザーがレポート用クライアント アプリケーションを使用してそのモデルに接続できます｡ 詳しくは、「[Azure Analysis Services にデプロイする](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy)」をご覧ください。  
  
このレッスンの推定所要時間: **5 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 参照してくださいこのレッスンの作業を実行する前に、前のレッスン「[レッスン 12: Excel で分析する](../tutorials/aas-lesson-12-analyze-in-excel.md)」を終えている必要があります。  

> [!IMPORTANT]  
> デプロイするには､リモート Analysis Services サーバーでの[管理者権限](../analysis-services-server-admins.md)が必要です。  

> [!IMPORTANT]  
> オンプレミスの SQL Server に AdventureWorksDW2014 サンプル データベースをインストールしていて､Azure Analysis Services サーバーにモデルをデプロイする場合は､[オンプレミスのデータ ゲートウェイ](../analysis-services-gateway.md)が必要です。
  
## <a name="deploy-the-model"></a>モデルをデプロイする  
  
#### <a name="to-configure-deployment-properties"></a>デプロイ関連のパラメータを設定する  

  
1.  **[Solution Explorer]** で **[AW Internet Sales]** プロジェクト を右クリックし､**[プロパティ]** をクリックします｡  
  
2.  **[AW Internet Sales Property Pages]** ダイアログ ボックスの **Deployment Server** にある **[Server]** プロパティで､フル サーバーを入力します。  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  **[Database]** プロパティで **Adventure Works Internet Sales** と入力します｡  
  
4.  **[Model Name]** プロパティで **Adventure Works Internet Sales Model** と入力します｡  
  
5.  選択内容を確認し､**[OK]** をクリックします｡  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Adventure Works Internet Sales をデプロイする
  
1.  **Solution Explorer** で **AW Internet Sales** プロジェクトを右クリックし､ **Build** を選択します｡  

2.  **AW Internet Sales** プロジェクトを右クリックし､**Deploy** を選択します｡

    Azure Analysis Services へのデプロイでは､アカウントの入力を求められることがあります｡ 社内でのアカウントとパスワードを入力します (例: nancy@adventureworks.com)｡このアカウントは､サーバーの管理者である必要があります。
  
    [Deploy] ダイアログ ボックスにモデルに含まれるメタデータと各テーブルのデプロイ状況が表示されます｡  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. デプロイが正常に終了したら､**[Close]**をクリックします｡  
  
## <a name="conclusion"></a>まとめ  
ご利用ありがとうございます。 これで､初めての Analysis Services 表形式モデルのオーサリングとデプロイは終了です｡ このチュートリアルでは､表形式モデルの作成で一般的な作業の手順をご案内しました｡ Adventure Works Internet Sales モデルをデプロイしましたから､SQL Server Management Studio を使用してモデルを管理､すなわち､処理スクリプトやバックアップ計画を作成することができます｡ またユーザーはこれで､Microsoft Excel や Power BI などのレポート用クライアント アプリケーションを使用してモデルに接続できます｡  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>次の手順
[Power BI Desktop を使用した接続](../analysis-services-connect-pbi.md)   
[補足のレッスン - 動的なセキュリティ](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[補足のレッスン - 詳細行](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[補足のレッスン - 不規則階層](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
