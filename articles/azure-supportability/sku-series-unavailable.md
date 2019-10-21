---
title: 利用できない SKU シリーズ | Microsoft Docs
description: 一部の SKU シリーズは、このリージョンでは、選択されたサブスクリプションでご利用いただけません。
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: e317ae1ad88cf162f1d55a06d19e7b3b0b88ce60
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249039"
---
# <a name="region-or-sku-unavailable"></a>リージョンまたは SKU が利用できない
この記事では、Azure サブスクリプションからリージョンまたは VM SKU を利用できない問題の解決方法について説明します。

## <a name="symptoms"></a>現象

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>仮想マシンをデプロイするときに、次のいずれかのエラー メッセージが表示されます。
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>予約仮想マシン インスタンスを購入するときに、次のいずれかのエラー メッセージが表示されます。

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>コンピューティング コア クォータを増やす Support request を作成しているのですが、リージョンまたは SKU ファミリーが選択肢として表示されません。

## <a name="solution"></a>解決策
まず、実際のビジネス ニーズを満たす代替のリージョンまたは SKU を検討するようお勧めします。 適切なリージョンまたは SKU が見つからない場合は、次の手順に従って "サブスクリプション管理" の [Support request](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) を作成してください。


- [基本] ページで、[問題の種類] を [サブスクリプションの管理] として選択し、サブスクリプションを選択して [次へ] をクリックします。

![[基本] ブレード](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   [問題] ページで、[問題の種類] を [一般的な質問] として選択します。
- [詳細] セクションに次の情報を入力します。
  - 仮想マシンをデプロイしようとしているのか、予約仮想マシン インスタンスを購入しようとしているのかを記述します。
  - デプロイまたは購入しようとしている仮想マシン インスタンスのリージョン、SKU、数を明記します。


![問題点](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   連絡先の詳細情報を入力し、[作成] をクリックします。

![連絡先情報](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>フィードバック
ご意見とご提案をお待ちしております。 あなたの [提案](https://feedback.azure.com/forums/266794-support-feedback)をお寄せください。 また、[Twitter](https://twitter.com/azuresupport) や [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure)でマイクロソフトの最新情報をご覧いただけます。

## <a name="learn-more"></a>詳細情報
[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq)

