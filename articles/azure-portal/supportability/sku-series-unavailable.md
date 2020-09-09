---
title: リージョンまたは SKU シリーズが利用できない - Azure
description: 一部の SKU シリーズは、このリージョンについて選択されたサブスクリプションでは使用できず、サブスクリプション管理のサポート リクエストが必要な場合があります。
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: 87533b2e0c1397d0b8b2e9a89450a80a75c5e348
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763824"
---
# <a name="region-or-sku-unavailable"></a>リージョンまたは SKU が利用できない

この記事では、Azure サブスクリプションからリージョンまたは VM SKU を利用できない問題の解決方法について説明します。

## <a name="symptoms"></a>現象

仮想マシンをデプロイするときに、次のいずれかのエラー メッセージが表示されます。

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

予約仮想マシン インスタンスを購入するときに、次のいずれかのエラー メッセージが表示されます。

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

コンピューティング コア クォータを引き上げるサポート リクエストを作成しているときに、リージョンまたは SKU ファミリが選択肢として表示されません。

## <a name="solution"></a>解決策

まず、実際のビジネス ニーズを満たす代替のリージョンまたは SKU を検討するようお勧めします。

適切なリージョンまたは SKU が見つからない場合は、次の手順に従って**サブスクリプション管理**の[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を作成してください。

1. [Azure portal](https://portal.azure.com) のメニューで、 **[ヘルプとサポート]** を選択します。 次に、 **[新しいサポート要求]** を選びます。

1. **[基本]** の **[問題の種類]** として **[サブスクリプション管理]** を選択します。

1. **[サブスクリプション]** を選択し、 **[概要]** に簡単な説明を入力します。

   ![[新しいサポート リクエスト] の [基本] タブ](./media/SKU-series-unavailable/support-request-basics.png)

1. **[問題の種類]** で、 **[問題の種類を選択してください]** を選択します。

1. **[問題の種類を選択してください]** で、オプションを選択します。たとえば、 **[サブスクリプションまたはリソースにアクセスできません]**  >  **[問題が一覧に含まれていない]** 。 **[保存]** を選択します。

   ![要求の問題を指定します](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. **ソリューション** を選択して、考えられる解決策を調査します。 必要に応じて、 **[次へ:詳細]** を選択して続行します。

1. お客様の連絡先情報と共に、提供できる追加情報を入力します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 情報を確認したら、 **[作成]** を選択して要求を作成します。

## <a name="send-us-your-suggestions"></a>ご提案をお寄せください

ご意見とご提案をお待ちしております。 あなたの [提案](https://feedback.azure.com/forums/266794-support-feedback)をお寄せください。 また、[Twitter](https://twitter.com/azuresupport) や [Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/products/azure)で Microsoft とやり取りすることもできます。

## <a name="learn-more"></a>詳細情報

[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq)
