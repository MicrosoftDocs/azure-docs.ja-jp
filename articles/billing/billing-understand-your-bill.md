---
title: "Azure の課金内容の確認 | Microsoft Docs"
description: "Azure サブスクリプションの使用状況と課金内容を確認し、理解する方法について説明します。"
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: erihur;genli
translationtype: Human Translation
ms.sourcegitcommit: 29a6290a19186d5bc8a07d87617b47d21529d6b7
ms.openlocfilehash: 2e99332e2a2b023c00669b3ad5ea07f0c791a805
ms.lasthandoff: 02/14/2017


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Microsoft Azure の課金内容の確認
Azure の課金内容を理解するには、料金の概要が記載された請求書のほかに、毎日の使用状況の詳細なファイルを確認します。 この記事では、請求書と毎日の使用状況ファイルに表示される主な用語について説明します。 ファイルの取得については、「[Azure の請求書と毎日の使用状況データを取得する方法](billing-download-azure-invoice-daily-usage-date.md)」をご覧ください。 無料試用版のサブスクリプションをご利用の場合は、毎日の使用状況に関する情報は取得できますが、請求書は発行されません。

Microsoft Azure サブスクリプションの課金額は、料金プランによって異なります。 Visual Studio Enterprise (MPN) サブスクライバーなどの一部の料金プランには、お客様のニーズに合わせて Azure サービスで利用できる月々のクレジットが含まれています。

前回の請求期間末の最大 24 時間分の使用状況が、現在の請求書に表示される場合があります。 また、米国以外のお客様向けの課金明細書に記載されている料金は、見積もりとしての使用を目的として提供されています。 銀行ではさまざまな費用に通貨換算率を適用しています。

## <a name="a-namepdfa-understand-your-invoice-pdf"></a><a name="pdf"></a> 請求書 (.pdf) を理解する
請求書には課金額の要約が表示されます。 請求書は、[Azure Portal](https://portal.azure.com) から Portable Document Format (.pdf) でダウンロードできます。 詳しくは、「[Azure の請求書と毎日の使用状況データを取得する方法](billing-download-azure-invoice-daily-usage-date.md)」をご覧ください。 

次のセクションでは、請求書に表示される主な用語と、各用語の説明を一覧に示します。

### <a name="account-information"></a>アカウント情報
アカウント情報セクションには、ユーザーの使用状況とプロファイルに関する情報が表示されます。

![ヘッダー](./media/billing-understand-your-bill/Header.png)

| 用語 | Description |
| --- | --- |
| 請求書番号 |追跡を目的とする一意の請求書の識別子 |
| 請求サイクル |この請求書に含まれる日付の範囲 |
| 請求日 |請求書が作成された日付 |
| 支払い方法 |アカウントで使用される支払いの種類 (請求書またはクレジット カード)。 クレジット カードを更新する場合は、「[Azure サブスクリプションの支払いに使用するクレジット カードを変更する方法](billing-how-to-change-credit-card.md)」を参照してください。 法人のお客様は、Azure サブスクリプションの支払いに、小切手、オーバーナイト小切手、電信送金など、請求書による支払い方法を利用できます。 [Azure の課金で請求書払いを使用する方法](https://azure.microsoft.com/pricing/invoicing/)に関するページを参照してください。 |
| 請求先 |Microsoft Azure の支払いをする住所。 請求情報を更新する方法については、「[Azure サブスクリプションの支払いに使用するクレジット カードを変更する方法](billing-how-to-change-credit-card.md)」をご覧ください。 |
| サブスクリプション プラン |購入したサブスクリプション プランの種類 (従量課金制、BizSpark Plus、Azure Pass など)。 従量課金制を別のプランに変更する場合は、「[別のプランへの Azure サブスクリプションの切り替え](billing-how-to-switch-azure-offer.md)」を参照してください。 |
| アカウント所有者メール アドレス |Microsoft Azure アカウントが登録されたアカウントのメール アドレス。 メール アドレスの変更については、「[Azure アカウントのプロファイル情報 (連絡先メール アドレス、住所、電話番号など) を変更する方法](billing-how-to-change-azure-account-profile.md)」を参照してください。 |

### <a name="understand-the-invoice-summary"></a>請求書の要約について
請求書の**請求書の要約**セクションでは、前回の請求以降の明細と現在の利用料金が要約されています。

![[請求書の要約]](./media/billing-understand-your-bill/InvoiceSummary.png)

請求書の繰越残高、入金額、および未払い残高のセクションでは、前回の請求書以降の明細が要約されています。

| 用語 | Description |
| --- | --- |
| 繰越残高 |前回の請求書の合計請求額 |
| 入金額 |前回の請求に対する合計支払い金額 |
| (前の請求サイクルからの) 未払い残高 |前回の請求以降にアカウントに適用された請求書の調整 (クレジットまたは残高) |

### <a name="understand-the-current-charges"></a>現在の料金について
請求書の現在の料金セクションには、月々の料金の詳細が表示されます。 

| 用語 | Description |
| --- | --- |
| 利用料金 |利用料金は、サブスクリプションの月額料金の合計です。 前月の利用量分が課金されます。 |
| 割引 |現在の請求書に適用されるサービス割引。 |
| 調整 |現在の請求書に適用されるその他のクレジットまたは未処理料金です。 たとえば、Visual Studio Enterprise with MSDN のプランをご利用の場合は、毎月のクレジット額が表示されます。 サブスクリプションをキャンセルすると、サブスクリプション プランで提供される毎月のクレジット額を超えた月額利用料金が表示されます。 料金は、現在の請求期間の開始日からサブスクリプションのキャンセル日までです。 |


### <a name="sold-to-and-payment-instructions"></a>販売先と支払い指示
次の表では、請求書に表示される販売先と支払い指示について説明します。

| 用語 | Description |
| --- | --- |
| 販売先 |ここには、アカウントのプロファイル住所があらかじめ入力されています。 変更する場合は、「[Azure アカウントのプロファイル情報 (連絡先メール アドレス、住所、電話番号など) を変更する方法](billing-how-to-change-azure-account-profile.md)」を参照してください。 |
| 支払い指示 |支払い方法が請求書の場合は、小切手の送信先、電信送金、またはオーバーナイト チェックに関する支払の指示になります。 詳細については、[Azure の課金で請求書払いを使用する方法](https://azure.microsoft.com/pricing/invoicing/)に関するページを参照してください。 |

## <a name="a-namecsva-understand-detailed-usage-charges-csv"></a><a name="csv"></a>詳細な利用料金について理解する (.csv)
使用状況ファイルには、現在の請求期間内に使用された各リソースの量が表示されます。 このファイルは、スプレッドシート アプリケーションで開くことができるコンマ区切り値 (.csv) ファイル形式で入手できます。 使用可能なバージョンが 2 つ表示される場合は、バージョン 2 をダウンロードします。 こちらが最新のファイル形式になります。 詳しくは、「[Azure の請求書と毎日の使用状況データを取得する方法](billing-download-azure-invoice-daily-usage-date.md)」をご覧ください。

利用料金は、サブスクリプションに対する合計**月額**料金にクレジットまたは割引を適用した金額になります。 前月の利用量分が課金されます。  

次のセクションでは、バージョン 2 の詳細な使用状況ファイルに表示される主な用語について説明します。

### <a name="statement"></a>ステートメント 
ファイルの最上部のセクションには、前の月の請求サイクルで使用したサービスが表示されます。 次の表では、このセクションに表示される用語と説明の一覧を示します。

| 用語 | 説明 |
| --- | --- |
|請求期間 |リソースまたはサービスが使用された請求期間。 |
|測定カテゴリ |この使用量の対象となる最上位レベルのサービスを示します。 |
|測定サブカテゴリ |Azure サービスの種類を定義します。この定義は料金に影響します。 |
|測定名 |消費しているリソースの測定単位を示します。 |
|測定リージョン |データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。 |
|SKU |各 Azure リソースの一意のシステム ID を示します。 |
|単位 |サービスが課金される単位を特定します。 たとえば、GB、時間、10,000 単位などです。 |
|消費量 |請求期間中に使用したリソースの量。 |
|含まれる量 |現在の請求期間内に無料で含まれるリソースの量。 |
|超過量 |消費量と含まれる量の差が表示されます。 この差異について課金されます。 プランに一定量が含まれない従量課金制では、この合計は消費量と同じになります。 |
|コミットメント以内 |6 か月プランまたは 12 か月プランに関連付けられたコミットメント額から差し引かれるリソース料金が表示されます。 リソース料金は発生順にコミットメント額から差し引かれます。 |
|通貨 |現在の請求期間で使用する通貨。 |
|超過料金 |6 か月プランまたは 12 か月プランに関連付けられたコミットメント額を超えたリソース料金が表示されます。 |
|コミットメント レート |6 か月プランまたは 12 か月プランに関連付けられたコミットメント額の合計に基づいてコミットメント レートが表示されます。 |
|料金 |課金対象項目ごとに請求される価格。 |
|値 |[超過量] 列に [単価] 列を掛けた料金を示します。 消費量が含まれる量を超過していない場合、この列の料金は発生しません。 |

### <a name="daily-usage"></a>毎日の使用状況 

ファイルの毎日の使用状況セクションには、請求金額に影響する使用状況の詳細が表示されます。 次の表では、このセクションに表示される用語と説明の一覧を示します。 

| 用語| 説明 |
| --- | --- |
|使用日 |リソースが使用された日付。 |
|測定カテゴリ |この使用量の対象となる最上位レベルのサービスを示します。 |
|測定 ID |課金測定の識別子です。 課金データの料金計算に使用される識別子です。 |
|測定サブカテゴリ |料金に影響する Azure サービスの種類を定義します。 |
|測定名 |消費しているリソースの測定単位を示します。 |
|測定リージョン|データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。 |
|単位 |サービスが課金される単位を特定します。 たとえば、GB、時間、10,000 単位などです。 |
|消費量 |その日に消費されたリソースの量。 |
|リソースの場所 |リソースが実行されているデータ センターを特定します。 |
|使用サービス |使用した Azure プラットフォーム サービス。 |
|リソース グループ |デプロイされたリソースが実行されるリソース グループ。 詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。 |
|インスタンス ID |リソースの識別子。 識別子には、リソースの作成時に指定した名前が含まれています。 リソース名または完全修飾リソース ID のいずれかです。 詳細については、[Azure Resource Manager API](/rest/api/resources/resources) に関するページを参照してください。 |
|タグ |この列には、ユーザーが指定したリソース タグが含まれます。 タグは、課金記録のグループ化に使用できます。 たとえば、タグを使用して、リソースを使用する部門ごとにコストを配分することができます。 タグの生成をサポートするサービスは、仮想マシン、記憶域、および [Azure Resource Manager API](/rest/api/resources/resources) を使用してプロビジョニングされたネットワーク サービスです。 詳細については、[タグを使用した Azure リソースの整理](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)に関するページを参照してください。 |
|追加情報 |サービス固有のメタデータ。 たとえば、仮想マシンのイメージの種類です。 |
|サービス情報 1 |サブスクリプションでサービスが属しているプロジェクトの名前。 |
|サービス情報 2 |これは、サービス固有の省略可能なメタデータをキャプチャする、以前から使用されているフィールドです。 |

## <a name="tips-for-cost-management"></a>コスト管理に関するヒント
コスト管理には積極的に取り組むことをお勧めします。

- [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)や[総保有コスト計算ツール](https://aka.ms/azure-tco-calculator)を使用してコストを見積もり、サービスを追加するときにもコストを見積もる
- [課金アラートのセットアップ](billing-set-up-alerts.md)
- [Azure Portal で使用状況とコストを定期的に確認する](billing-getting-started.md#costs)

詳細については、「[Azure の課金とコスト管理の概要](billing-getting-started.md)」をご覧ください。

## <a name="how-do-i-make-a-payment"></a>支払い方法を教えてください。
支払い方法としてクレジット カードやデビット カードを設定している場合、支払いは自動的に行われます。 [支払い方法として請求書](https://azure.microsoft.com/pricing/invoicing/)を利用するように設定している場合、請求書の下部に記載されている場所に支払いを送付します。 詳しくは、 [サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>クレジット カードによる支払い状況を確認するにはどうすればよいですか。
[サポート チケットを作成](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)して、支払い状況の確認に関するサポートを要求してください。 

## <a name="what-about-marketplace-orders-or-external-service-charges"></a>Marketplace での注文や外部サービスの課金について教えてください。
外部サービスは、以前は Marketplace と呼ばれていました。 外部サービスは、独立系サービス ベンダーによって提供されますが、Azure エコシステムに統合されます。 詳細については、「[Azure 外部サービスの課金について](billing-understand-your-azure-marketplace-charges.md)」を参照してください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。 
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
 




