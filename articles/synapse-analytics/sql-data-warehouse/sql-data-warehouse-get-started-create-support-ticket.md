---
title: クォータの増加を要求し、サポートを受ける
description: Azure portal で Azure Synapse Analytics のサポート リクエストを作成する方法について説明します。 クォータの増加を要求するか、問題解決へのサポートを受けます。
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: gaursa
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b5b7563b49c6f0eb155223026797f8306e642f5a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585657"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Azure Synapse Analytics のクォータの増加を要求し、サポートを受ける

この記事では、Azure portal で Azure Synapse Analytics のサポート チケットを送信する方法について説明します。 このプロセスでは、クォータの増加を要求したり、エンジニアリング サポート チームのテクニカル サポート リクエストを送信したりできます。

## <a name="create-a-support-ticket"></a>サポート チケットの作成

Azure portal から Azure Synapse Analytics の新しいサポート要求を作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) のメニューで、 **[ヘルプとサポート]** を選択します。

   ![[ヘルプとサポート] リンク](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. **[ヘルプとサポート]** で、 **[新しいサポート リクエスト]** を選択します

    ![新しいサポート リクエストを作成する](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. お使いの [Azure サポート プラン](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)を確認します。

   * **課金、クォータ、サブスクリプション管理** のサポートは、すべてのサポート レベルでご利用いただけます。
   * **障害対応** のサポートは、[Developer](https://azure.microsoft.com/support/plans/developer/)、[Standard](https://azure.microsoft.com/support/plans/standard/)、[Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)、または [Premier](https://azure.microsoft.com/support/plans/premier/) サポートを通して提供されます。 障害対応のサポートとは、Azure のご利用に際して発生した、マイクロソフトが原因と想定される問題への対応です。
   * **開発者支援** および **アドバイザリ サービス** は、[Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) および [Premier](https://azure.microsoft.com/support/plans/premier/) サポート レベルでご利用いただけます。

   Premier サポート プランを使用している場合は、[Microsoft Premier オンライン ポータル](https://premier.microsoft.com/)で Azure Synapse Analytics 関連の問題を報告することもできます。 各種のサポート プランの詳細 (範囲、応答時間、価格などを含む) については、[Azure サポート プラン](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)に関するページを参照してください。Azure サポートに関するよく寄せられる質問については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

1. **[問題の種類]** で、適切な問題の種類を選択します。 障害対応の問題の場合は、 **[技術]** を選択します。 クォータの増加の要求の場合は、 **[サービスとサブスクリプションの制限 (クォータ)** ] を選択します。

   ![問題の種類を選択する](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > この記事の残りの部分では、クォータ増加の要求に焦点を当てます。 ただし、ここで **[技術]** を選択することで、問題解決のサポート リクエストを行うことも出来ます。 **[技術]** を選択した場合は、概要を入力してから、 **[問題の種類を選択してください]** を選択して問題の種類を特定するよう求められます。 問題の解決に役立つ解決策が表示される場合があります。 提示された解決策で問題が解決しない場合は、 **[次へ: 詳細]** を選択してフォームに記入し、サポート チケットを送信します。

1. クォータの増加の要求の場合は、 **[クォータの種類]** で **[Azure Synapse Analytics]** を選択します。 次に、 **[次のステップ: ソリューション >>]** を選択します。

   ![クォータの種類を選択する](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. **[詳細]** ウィンドウで、 **[詳細を入力]** を選択して追加情報を入力します。

   ![[詳細の指定] リンク](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>クォータ要求の種類

**[詳細を入力]** を選択すると、 **[クォータの詳細]** ウィンドウが表示され、追加情報を追加できるようになります。 以下のセクションでは、Azure Synapse Analytics で使用できるさまざまなクォータ要求について説明します。

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>サーバーあたりの Synapse SQL プールの Data Warehouse ユニット (DWU)

サーバーあたりの DWU の増加を要求するには、次の手順に従います。

1. クォータの種類として **[Synapse SQL pool DWUs per server]** \(サーバーあたりの Synapse SQL プールの DWU\) を選択します。

1. ドロップダウンリストを使用して、クォータの増加を適用する **[リソース]** を選択します。

1. **[クォータの要求]** セクションに新しいクォータを入力します。

1. **[Save and continue]\(保存して続行\)** を選択します。

   ![DWU のクォータの詳細](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>サブスクリプションあたりのサーバー

サブスクリプションあたりのサーバー数の増加を要求するには、次の手順を完了する必要があります。

1. クォータの種類として **[サブスクリプションあたりの SQL Server]** を選択します。

1. **[場所]** リストで、使用する Azure リージョンを選択します。 クォータは、各リージョンのサブスクリプション単位となります。

1. **[クォータの要求]** フィールドに、そのリージョン内で要求する最大サーバー数を入力します。

   ![サーバー クォータの詳細](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. **[Save and continue]\(保存して続行\)** を選択します。

一部のオファーの種類は、すべてのリージョンで使用できるわけではありません。 次のエラーがに表示される場合があります。

![リージョンへのアクセス エラー](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>リージョンへのサブスクリプション アクセスを有効にする

サブスクリプションのリージョンへのアクセスを有効にするには、次の手順を完了する必要があります。  

1. クォータの種類として **[Synapse SQL プール (データ ウェアハウス) のリージョン アクセス]** を選択します。

1. ドロップダウンリストから **[場所]** を選択して、リージョンを選択します。

1. **[DWU 必須]** セクションで DWU のパフォーマンス要件を指定します。

1. **[ビジネス要件の説明]** を入力します。 

1. **[Save and continue]\(保存して続行\)** を選択します。

![リージョン アクセス](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>その他のクォータ要求の場合

その他のクォータ要求の種類の場合は、[クォータの種類] ドロップダウン メニューから **[その他のクォータ要求]** を選択します。

![その他のクォータの詳細](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>要求を送信する

最後の手順として、SQL Database サポート リクエストの残りの詳細を入力します。 次に、 **[次のステップ: 確認と作成 >>]** を選択します。

![確認と作成の詳細](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

要求の詳細を確認したら、 **[作成]** を選択して要求を送信します。

![チケットの作成](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>サポート チケットの状態の確認

サポート要求を送信すると、Azure サポート チームから連絡があります。 要求の状態と詳細を確認するには、ダッシュボードで **[すべてのサポート要求]** を選択します。

![状態の確認](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>その他のリソース

[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) や [Azure Synapse Analytics の Microsoft Q&A 質問ページ](/answers/topics/azure-synapse-analytics.html)で Azure Synapse Analytics のコミュニティとつながることもできます。