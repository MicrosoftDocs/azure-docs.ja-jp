---
title: 標準契約 | Azure
description: Azure Marketplace と AppSource の標準契約
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620379"
---
# <a name="standard-contract"></a>標準契約

顧客の調達プロセスを簡略化し、ソフトウェア ベンダーの法的な複雑さを軽減するために、Microsoft では、マーケットプレースでの取引の促進に役立つ標準契約テンプレートを用意しています。 Azure Marketplace の発行元は、カスタムの使用条件を作成するのではなく、標準契約の下で各自のソフトウェアを提供することを選択でき、顧客に必要なのは、それを入念に調べて 1 度承認することだけです。 標準契約はこちらで見つけることができます[https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)。 

オファーの使用条件は、クラウド パートナー ポータルでオファーを作成するときに、[Marketplace] タブで定義します。 [標準契約] オプションは、その設定を [はい] に変更することで有効になります。

![[標準契約] オプションの有効化](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>標準契約を使用することを選択した場合でも、[クラウド ソリューション プロバイダー](./cloud-solution-providers.md) チャネル用の別個の使用条件は引き続き必要です。

## <a name="standard-contract-amendments"></a>Standard Contract Amendments (標準契約の修正)

発行元は、[Standard Contract Amendments]\(標準契約の修正\) を使用して簡単に標準契約を選択できます。また、製品やビジネスに合わせてカスタマイズした用語を使用できます。  Microsoft 標準契約を既に確認し、同意している場合、お客様に必要なことは契約の修正の確認のみです。

Azure Marketplace の発行元には 2 種類の修正があります。

* Universal Amendments (ユニバーサル修正):これらの修正は、すべての顧客の標準契約に例外なく適用されます。 ユニバーサル修正は、購入フロー内の製品のすべてのお客様に示されます。

![Universal Amendments (ユニバーサル修正)](media/marketplace-publishers-guide/universal-amendaments.png)

* Custom Amendments (カスタム修正):Azure Marketplace には、テナントを対象としたカスタム修正の条項もあります。 これらは特定のお客様のみを対象とした標準契約の特別な修正です。 発行元は、対象にするテナントを選択できます。 そのテナントのお客様は、標準契約と対象となる修正に従って製品を購入します。

![Custom Amendments (カスタム修正)](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>カスタム修正の対象となるお客様には、購入時に標準条項に対するユニバーサル修正も適用されます。

>[!Note]
>次のオファーの種類が [Standard Contract Amendments]\(標準契約の修正\) をサポートしています。Azure アプリケーション (ソリューション テンプレートとマネージド アプリケーション)、仮想マシン、コンテナー、コンテナー アプリケーション。

### <a name="customer-experience"></a>カスタマー エクスペリエンス

Azure portal での購入プロセス中に、お客様は Microsoft 標準契約とその修正として製品に関連するご契約条件を確認することができます。

![Azure portal のカスタマー エクスペリエンス。](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

お客様は、`Get-AzureRmMarketplaceTerms` を使用してオファーのご契約条件を取得し、同意することができます。 標準契約と関連する修正は、コマンドレットの出力で返されます。

---
