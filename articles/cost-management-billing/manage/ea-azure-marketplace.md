---
title: Azure Marketplace
description: EA のお客様が Azure Marketplace を使用する方法について説明します
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 91079310d4d566a65c6e8bc7f913ba92d04dece1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689934"
---
# <a name="azure-marketplace"></a>Azure Marketplace

この記事では、EA のお客様とパートナーがマーケットプレースの料金を確認し、Azure Marketplace で購入したものを有効にする方法について説明します。

## <a name="azure-marketplace-for-ea-customers"></a>EA お客様向け Azure Marketplace

ダイレクトのお客様の場合、Azure Marketplace の料金は Azure エンタープライズ ポータルに表示されます。 Azure Marketplace での購入および消費については、Azure 前払いとは別に、四半期ごとまたは月単位で未払い分が請求されます。

インダイレクトのお客様は、Azure エンタープライズ ポータルの **[サブスクリプションの管理]** ページで Azure Marketplace のサブスクリプションを確認できますが、価格は非表示になります。 Azure Marketplace の料金については、ライセンス ソリューション プロバイダー (LSP) にお問い合わせください。

新規の月単位または年単位の定期的な Azure Marketplace での購入については、Azure Marketplace 項目が購入された期間の全額が請求されます。 これらの項目は、次の期間の最初の購入と同じ日に自動更新されます。

既存の、毎月の定期的な料金は引き続き、各カレンダー月の最初の日に更新されます。 年間料金は、購入日の応当日に更新されます。

Azure Marketplace で利用可能な一部のサードパーティ リセラー サービスでは、エンタープライズ契約 (EA) の Azure 前払い残高が消費されるようになりました。 従来、これらのサービスは EA Azure 前払い以外で課金され、個別に請求されていました。 Azure Marketplace のこれらのサービスの EA Azure 前払いは、お客様の購入と支払管理を簡素化するのに役立ちます。 Azure 前払いが消費されるようになったサービスの完全なリストについては、[Azure Web サイト (更新日: 2018 年 3 月 6 日)](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/) を参照してください。

### <a name="partners"></a>パートナー

LSP は、Azure エンタープライズ ポータルの価格シート ページから、Azure Marketplace の価格表をダウンロードすることができます。 右上にある **[Marketplace Price list]\(Marketplace の価格表\)** リンクを選択します。 Azure Marketplace の価格表には、利用可能なすべてのサービスとその価格が表示されます。

価格表をダウンロードするには、次のようにします。

1. Azure エンタープライズ ポータルで、 **[レポート]**  >  **[価格シート]** の順に移動します。
1. 右上隅のユーザー名の下にある Azure Marketplace の価格表へのリンクを見つけます。
1. リンクを右クリックして、 **[対象をファイルに保存]** を選択します。
1. **[保存]** ウィンドウで、ドキュメントのタイトルを `AzureMarketplacePricelist.zip` に変更します。これにより、ファイルが .xlsx から .zip ファイルに変更されます。
1. ダウンロードが完了した後、国固有の価格表を含む zip ファイルを入手できます。
1. LSP は、国固有の価格について、個々の国ファイルを参照する必要があります。 LSP は **[通知]** タブを使用して、まったく新しい、または廃止された SKU を確認できます。
1. 料金が頻繁に変わることはありません。 LSP には、メールで 30 日前に値上げと外国為替 (FX) の変更が通知されます。
1. LSP は、加入契約、ISV、および四半期ごとに 1 枚の請求書を受け取ります。

### <a name="enabling-azure-marketplace-purchases"></a>Azure Marketplace での購入の有効化

エンタープライズ管理者は、その加入契約下のすべての Azure サブスクリプションに対して、Azure Marketplace での購入を無効または有効にすることができます。 エンタープライズ管理者が購入を無効にし、既に Azure Marketplace サブスクリプションが含まれている Azure サブスクリプションがある場合は、それらの Azure Marketplace サブスクリプションが取り消されたり、影響を受けたりすることはありません。

お客様は Azure エンタープライズ ポータルで加入契約に関連付けることで、ダイレクト Azure サブスクリプションを Azure EA に変換することはできますが、このアクションでは、子のサブスクリプションが自動的に変換されません。

Azure Marketplace での購入を有効にするには、次のようにします。

1. エンタープライズ管理者として、Azure エンタープライズ ポータルにサインインします。
1. **[管理]** に移動します。
1. **[加入契約の詳細]** で、 **[Azure Marketplace]** 品目の横にある鉛筆アイコンを選択します。
1. **有効または無効**、あるいは必要に応じて、無料の **BYOL SKU のみ** に切り替えます。
1. **[保存]** を選択します。

> [!NOTE]
> BYOL (ライセンス持ち込み) および無料のみのオプションでは、Azure Marketplace SKU の購入と取得が BYOL と無料の SKU のみに制限されます。

### <a name="services-billed-hourly-for-azure-ea"></a>Azure EA に対して時間単位で課金されるサービス

次のサービスについては、MOSP での月額料金ではなく、エンタープライズ契約で 1 時間ごとに課金されます。

- Application Delivery Network
- Web アプリケーション ファイアウォール

### <a name="azure-remoteapp"></a>Azure RemoteApp

エンタープライズ契約のお客様は、エンタープライズ契約の価格レベルに基づいて、Azure RemoteApp の料金をお支払いただきます。 追加料金はかかりません。 標準価格には最初の 40 時間が含まれます。 無制限の価格では、最初の 80 時間が対象となります。 RemoteApp では、80 時間を超える使用量の生成が停止されます。

## <a name="azure-marketplace-faq"></a>Azure Marketplace に関する FAQ

このセクションでは、Azure Marketplace で一部のサードパーティ リセラー サービスに Azure 前払いがどのように適用される可能性があるかについて説明します。

### <a name="what-changed-with-azure-marketplace-services-and-azure-ea-prepayment"></a>Azure Marketplace サービスと Azure EA 前払いに関してはどのような変更がありますか?

2018 年 3 月 1 日より、一部のサードパーティ リセラー サービスで、Azure EA 前払いが使用されています。 Azure 予約 VM インスタンス (RI) を除き、サービスは以前は、Azure EA 前払いとは別に課金され、個別に請求されていました。

弊社では Azure 前払いの使用を拡大し、最も頻繁に購入される、サードパーティが公開している Azure Marketplace サービスの一部が含まれるようにしました。 Azure Marketplace のこれらのサービスの Azure EA 前払いは、購入と支払管理を簡素化するのに役立ちます。

### <a name="why-did-we-make-this-change"></a>なぜこの変更が行われたのですか?

お客様は、Azure 前払いを利用するための追加の方法を絶えず探しています。 この変更はお客様によって頻繁に要求されたものであり、Azure Marketplace のお客様の大部分に影響を与えました。

### <a name="how-do-you-benefit"></a>どのようなメリットがありますか?

よりシンプルな課金エクスペリエンスが得られ、Azure EA 前払いを利用しやすくなります。 これらのサービスは Azure 前払いに含まれているため、Azure EA 前払いはより価値のあるものになります。

### <a name="what-azure-marketplace-services-use-azure-ea-prepayment-and-how-do-i-know"></a>Azure EA 前払いを使用する Azure Marketplace サービスは何ですか? また、どうすれば確認できますか?

Azure 前払いを使用するサービスを購入すると、Azure Marketplace で免責事項が示されます。 Red Hat、SUSE、Autodesk、および Oracle によって公開されている、いくつかのサービスがサポートされます。 現在、他のパーティによって公開されている同じような名前のサービスは、Azure 前払いから差し引かれません。 完全なリストはこの FAQ の最後にあります。

### <a name="what-if-my-azure-ea-prepayment-runs-out"></a>Azure EA 前払いで超過が発生した場合はどうすればよいですか?

すべての Azure 前払いを消費し、超過した場合、これらのサービスに関連する料金が、その他の従量課金サービスと共に次の超過分の請求書に表示されます。 2018 年 3 月 1 日の変更の前までは、これらの料金は他の Azure Marketplace サービスと共に請求されました。

### <a name="why-dont-all-azure-marketplaces-consume-azure-ea-prepayment"></a>すべての Azure Marketplace で Azure EA 前払いが消費されないのはなぜですか?

弊社は、Azure EA 前払いに関連する最高のカスタマー エクスペリエンスを提供するための取り組みを頻繁に行っています。 この変更により、Azure Marketplace における多くのお客様と総支出の大部分に対応できました。 その他のサービスは今後、追加される可能性があります。

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>これはダイレクトの加入契約とパートナーにどのように影響しますか?

ダイレクトのお客様やパートナーには影響しません。 これらのサービスは、他の従量課金サービスと同じパートナー マークアップ機能の対象となります。 唯一の変更点は、料金が異なる請求書に表示されることと、料金の支払いがお客様の Azure EA 前払いからであることです。

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-azure-ea-prepayment"></a>Azure EA 前払いを消費する Azure Marketplace サービスのリストはありますか?

特定の Azure Marketplace オファーでは、Azure 前払い資金を使用できます。 このプログラムに参加している製品の完全なリストについては、[Azure 前払いを使用するサードパーティ サービス](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

- [価格](ea-pricing-overview.md)の詳細を確認します。