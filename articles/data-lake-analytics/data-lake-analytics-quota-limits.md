---
title: "Azure Data Lake Analytics のクォータ制限 | Microsoft Docs"
description: "Azure Data Lake Analytics (ADLA) アカウントのクォータ制限を調整する方法と引き上げる方法について説明します。 これらの制限について把握すれば、U-SQL ジョブの動作の理解に役立つ可能性があります。 これらの制限はいずれも固定ではなく、Microsoft への連絡を通じていつでも上限を引き上げることができます。"
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/16/2016
ms.author: omidm
translationtype: Human Translation
ms.sourcegitcommit: ce76fb1feaa38ff20ccc873114541a015126a1ad
ms.openlocfilehash: cffafbc20294c235060b03a75f6d1bee712115d1


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Azure Data Lake Analytics のクォータ制限
Azure Data Lake Analytics (ADLA) アカウントのクォータ制限を調整する方法と引き上げる方法について説明します。 これらの制限について把握すれば、U-SQL ジョブの動作の理解に役立つ可能性があります。 これらの制限はいずれも固定ではなく、Microsoft への連絡を通じていつでも上限を引き上げることができます。

**前提条件**

このチュートリアルを開始する前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Data Lake Analytics アカウント**。 「[Azure Portal で Azure Data Lake Analytics の使用を開始する](https://azure.microsoft.com/en-us/documentation/articles/data-lake-analytics-get-started-portal/#create-adl-analytics-account)」を参照してください。
* **Data Lake Analytics ジョブ プロセスの基本的な知識**。 「[Azure Portal で Azure Data Lake Analytics の使用を開始する](https://azure.microsoft.com/en-us/documentation/articles/data-lake-analytics-get-started-portal/)」を参照してください。

<!-- ################################ -->
<!-- ################################ -->
## <a name="quota-limits"></a>クォータ制限:
ここでは、システムの現在のクォータ制限について概説します。

**Azure サブスクリプションの制限:** Azure サブスクリプションには次の制限が適用されます。
* **サブスクリプションあたりの ADLA アカウント数:** 5。 これは、サブスクリプションごとに作成できる ADLA アカウントの最大数です。 6 番目の ADLA アカウントを作成しようとすると、エラー "You have reached the maximum number of Data Lake Analytics accounts allowed (5) in region under subscription name (リージョンの <サブスクリプション名> で許可される Data Lake Analytics アカウントの最大数 (5) に達しました)" が表示されます。 このエラーは、サブスクリプションで使用している ADLA アカウントを削除するか、サポート チケットを開いて Microsoft に連絡することで、簡単に修正できます。

**ADLA アカウントの制限:**
* **アカウントあたりの Analytics ユニット (AU) の最大数:** 250。 これは、アカウントで同時に実行できる AU の最大数です。 すべてのジョブで実行中の AU の合計数がこの数を超えることはできません。 この値を超えると、新しいジョブは自動的にキューに登録されます。 次に例を示します。
    * 250 個の AU を使用する 1 つのジョブが実行されているときに 2 つ目のジョブを送信した場合、そのジョブは 1 つ目のジョブが完了するまでジョブ キューに登録されます。
    * それぞれ 50 個の AU を使用する 5 つのジョブが既に実行されているときに 20 個の AU を使用する 6 番目のジョブを送信した場合、このジョブはジョブ キューで待機状態となり、20 個の AU が使用可能になった時点で実行が開始されます。
* **アカウントあたりの同時実行 U-SQL ジョブの最大数:** 20。 これは、アカウントで同時に実行できるジョブの最大数です。 この値を超えると、新しいジョブは自動的にキューに登録されます。

**アカウントあたりの ADLA クォータ制限を調整するには:**
1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. 作成済みの ADLA アカウントを選択します。
3. **[プロパティ]** をクリックします。
4. ニーズに合わせて **[並行処理]** と **[最大同時ジョブ数]** を調整します。

    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

**最大クォータ制限を増やすには:**
1. Azure Portal でサポート要求を開きます。

    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. [問題の種類] で **[クォータ]** を選択します。
3. **サブスクリプション**を選択します ("試用" サブスクリプションではないことを確認してください)。
4. [クォータの種類] で **[Data Lake Analytics]** を選択します。

    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5.  [問題] ブレードで、希望の上限と、この追加容量が必要な**詳しい理由**を入力します。

    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6.  自分の連絡先情報を確認し、サポート要求を作成します。

Microsoft では、お客様の依頼を確認したうえで、早急にビジネス ニーズに対応するよう努めます。

## <a name="see-also"></a>関連項目
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
* [Azure PowerShell を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-powershell.md)
* [Azure ポータルを使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)



<!--HONumber=Nov16_HO3-->


