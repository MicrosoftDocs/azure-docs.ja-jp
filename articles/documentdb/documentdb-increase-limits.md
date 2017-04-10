---
title: "DocumentDB アカウント クォータの引き上げ要求 | Microsoft Docs"
description: "コレクションあたりのドキュメント ストレージやスループットなどの、DocumentDB データベース のクォータの調整を要求する方法について説明します。"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 68f7dc8d-534f-4301-a42c-bcd1bb1b77fe
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 803d295f0d9f08fdfe7ee4550bd093b98bd80631
ms.openlocfilehash: 594f8c79e6e98a3e727416b0bf1867481b33dd2c


---
# <a name="request-increased-documentdb-account-quotas"></a>DocumentDB アカウント クォータの引き上げを要求する
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)は世界規模のデータベースであり、アプリケーションで要求されることをすべて処理するようにスループットとストレージをスケールできます。 DocumentDB が提供するスケールについて質問がある場合は、askdocdb@microsoft.com. に電子メールをお送りください。

この記事では、Azure のサポートによるクォータの引き上げを要求する方法について説明します。

## <a name="a-idrequestquotaincreasea-request-a-quota-adjustment"></a><a id="RequestQuotaIncrease"></a> クォータ調整の要求
クォータ調整を要求するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) で、**[その他のサービス]**、**[ヘルプとサポート]** の順にクリックします。
   
    ![ヘルプとサポート起動時のスクリーンショット](media/documentdb-increase-limits/helpsupport.png)
2. **[ヘルプとサポート]** ブレードで **[新しいサポート要求]** をクリックします。
   
    ![サポート チケット作成のスクリーンショット](media/documentdb-increase-limits/getsupport.png)
3. **[新しいサポート要求]** ブレードで、**[基本]** をクリックします。 次に、**[問題の種類]** を **[クォータ]** に、**[サブスクリプション]** を自分の DocumentDB アカウントをホストするサブスクリプションに、**[クォータの種類]** を **[DocumentDB]** に、**[サポート プラン]** を **[Quota SUPPORT - Included (クォータ サポート - 含む)]** に設定します。 次に、 **[次へ]**をクリックします。
   
    ![サポート チケットのリクエストの種類のスクリーンショット](media/documentdb-increase-limits/supportrequest1.png)
4. **[問題]** ブレードで重大度を選択し、クォータの増大に関する情報を **[詳細]** に入力します。 **[次へ]**をクリックします。
   
    ![サポート チケットのサブスクリプション選択のスクリーンショット](media/documentdb-increase-limits/supportrequest2.png)
5. 最後に、連絡先情報を **[連絡先情報]** ブレードに入力し、**[作成]** をクリックします。

サポート チケットが作成されると、サポート要求番号が電子メールで届きます。  サポート要求は、**[ヘルプとサポート]** ブレードの **[サポート要求の管理]** をクリックして表示することもできます。

![サポート リクエスト ブレードのスクリーンショット](media/documentdb-increase-limits/supportrequest4.png)

## <a name="a-namenextstepsa-next-steps"></a><a name="NextSteps"></a> 次のステップ
* DocumentDB の詳細については、 [ここ](http://azure.com/docdb)をクリックしてください。




<!--HONumber=Dec16_HO3-->


