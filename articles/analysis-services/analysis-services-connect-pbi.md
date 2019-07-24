---
title: Power BI で Azure Analysis Services に接続する | Microsoft Docs
description: Azure Analysis Services サーバーに Power BI を使って接続する方法を説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cb93c29ae4cb8d365cd128c52ee726c10c94bc19
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189339"
---
# <a name="connect-with-power-bi"></a>Power BI を使用した接続

Azure でサーバーを作成して、表形式モデルを展開すると、社内のユーザーはすぐに接続してデータの分析を開始できるようになります。 

> [!TIP]
> 必ず最新バージョンの [Power BI Desktop](https://powerbi.microsoft.com/desktop/) を使用してください。
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop での接続

1. Power BI Desktop で、**[データの取得]** > **[Azure]** > **[Azure Analysis Services データベース]** の順にクリックします。

2. **[サーバー]** にサーバー名を入力します。 必ず URL 全体を指定してください。たとえば、「asazure://westcentralus.asazure.windows.net/advworks」のように入力します。

3. **[データベース]** には、接続先の表形式モデル データベースまたはパースペクティブの名前がわかっている場合はその名前を貼り付けます。 それ以外の場合は、このフィールドを空のままにし、後からデータベースまたはパースペクティブを選択することができます。

4. 接続オプションを選択し、**[接続]** を押します。 

    **[ライブ接続]** と **[インポート]** の両方のオプションがサポートされています。 ただし、インポート モードにはいくつかの制限があるため、ライブ接続を使用することをお勧めします。特に、インポート時にはサーバーのパフォーマンスが影響を受ける可能性があります。 また、Power BI サービスでモデルを更新する場合、**[Power BI からのアクセスを許可する]** 設定は、**[ライブ接続]** を選択した場合にのみ適用されます。

5. メッセージが表示されたら、ログイン資格情報を入力します。 

6. **[ナビゲーター]** で、サーバーを展開し、接続先のモデルまたはパースペクティブを選択してから **[接続]** をクリックします。 モデルまたはパースペクティブをクリックすると、そのビューのすべてのオブジェクトが表示されます。

    Power BI Desktop でモデルが開き、[レポート] ビューに空のレポートが表示されます。 [フィールド] リストには、非表示にされているものを除くすべてのモデル オブジェクトが表示されます。 右下隅に接続ステータスが表示されます。

## <a name="connect-in-power-bi-service"></a>Power BI (サービス) での接続

1. サーバー上のモデルにライブ接続できる Power BI Desktop ファイルを作成します。
2. [Power BI](https://powerbi.microsoft.com) で、**[データの取得]** > **[ファイル]** をクリックしてから、お客様の .pbix ファイルを検索して選択します。

## <a name="see-also"></a>関連項目
[Azure Analysis Services に接続する](analysis-services-connect.md)   
[クライアント ライブラリ](analysis-services-data-providers.md)

