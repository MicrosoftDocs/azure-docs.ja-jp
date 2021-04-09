---
title: Azure Data Lake Analytics のクォータと 制限を調整します
description: Azure Data Lake Analytics (ADLA) アカウントのクォータ制限を調整する方法と引き上げる方法を説明します。
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 03/15/2018
ms.openlocfilehash: 416b77fd86316acb8098531257e12b591a0ff052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710177"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics のクォータと 制限を調整します

Azure Data Lake Analytics (ADLA) アカウントのクォータ制限を調整する方法と引き上げる方法を説明します。 これらの制限を認識していると、U-SQL ジョブの動作を理解するのに役立ちます。 すべてのクォータ制限は固定ではないため、Azure サポートに連絡して上限を引き上げることができます。

## <a name="azure-subscriptions-limits"></a>Azure サブスクリプションの制限

**各リージョンのサブスクリプションあたりの ADLA アカウントの最大数:** 5

6 番目の ADLA アカウントを作成しようとすると、エラー "サブスクリプション name のリージョンで許容される Data Lake Analytics アカウントの最大数 (5) に達しました。" が表示されます。

この制限を超える数にするには、次の方法を試すことができます。

- 適切であれば、別のリージョンを作成する
- [サポート チケットを開き](#increase-maximum-quota-limits)、Azure サポートにクォータの増加を依頼する

## <a name="default-adla-account-limits"></a>既定の ADLA アカウントの制限

**アカウントあたりの分析ユニット (AU) の最大数:** 250 (既定では 32)

これは、アカウントで同時に実行できる AU の最大数です。 すべてのジョブで実行されている AU の総数がこの制限を超えている場合は、新しいジョブが自動的にキューに挿入されます。 次に例を示します。

- 32 個の AU を使用する 1 つのジョブのみ実行されているときに 2 つ目のジョブを送信した場合、そのジョブは 1 つ目のジョブが完了するまでジョブ キューで待機します。
- 実行しているジョブが既に 4 つあり、それぞれが 8 AU を使用している場合に、8 個の AU 必要とする 5 番目のジョブを送信すると、そのジョブは、8 個の AU が使用可能になるまでジョブ キューで待機します。

    ![Azure Data Lake Analytics の制限とクォータのページ](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**ジョブあたりの分析ユニット (AU) の最大数:** 250 (既定では 32)

これは、アカウントで個別のジョブを割り当てることができる AU の最大数です。 ジョブあたりでさらに多くの AU を割り当てるコンピューティング ポリシー (ジョブ送信制限) が送信元に適用されない限り、この限度を超えて割り当てられたジョブは拒否されます。 この値の上限は、アカウントの AU の制限です。

**アカウントあたりの同時実行 U-SQL ジョブの最大数:** 20

これは、アカウントで同時に実行できるジョブの最大数です。 この値を超えると、新しいジョブは自動的にキューイングされます。

## <a name="adjust-adla-account-limits"></a>ADLA アカウントの制限を調整する

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. 既存の ADLA アカウントを選びます。
3. **[プロパティ]** をクリックします。
4. **最大 AU**、**実行ジョブの最大数**、**ジョブ送信の制限** の値を必要に応じて調整します。

## <a name="increase-maximum-quota-limits"></a>最大クォータ制限を増やす

[Azure サービス固有の制限に関するドキュメント](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits)に、Azure 制限に関する詳細があります。

1. Azure Portal でサポート要求を開きます。

   ![Azure Data Lake Analytics ポータル - ヘルプとサポート](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

   ![Azure Data Lake Analytics ポータルの新しいサポート リクエスト](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)

2. [問題の種類] で **[クォータ]** を選択します。

3. **サブスクリプション** を選択します ("試用" サブスクリプションではないことをご確認ください)。

4. [クォータの種類] で **[Data Lake Analytics]** を選択します。

   ![Azure Data Lake Analytics サポート リクエストのクォータの種類](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. [問題] ページで、希望の上限と、この追加容量が必要な理由の **詳細** を入力します。

   ![Azure Data Lake Analytics サポート リクエストの詳細](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. 自分の連絡先情報を確認し、サポート要求を作成します。

Microsoft で要求を確認し、お客様のビジネス ニーズにできるだけ早く対応できるよう努めます。

## <a name="next-steps"></a>次のステップ

- [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
- [Azure PowerShell を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-powershell.md)
- [Azure Portal を使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
