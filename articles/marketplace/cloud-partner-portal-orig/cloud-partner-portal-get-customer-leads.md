---
title: リードを構成する | Microsoft Docs
description: Cloud パートナー ポータルでリードを構成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a425e607ea7dac394ab90a3fed4d4026056bbc1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118580"
---
<a name="get-customer-leads"></a>潜在顧客の獲得
==================

この記事では、Cloud パートナー ポータルを使用してリードを作成する方法について説明します。 これらのリードを CRM システムに接続し、販売パイプラインに統合できます。

## <a name="leads"></a>リード

リードとは、[Azure Marketplace](https://azuremarketplace.microsoft.com/) の製品または [AppSource](https://appsource.microsoft.com) の製品に関心を示している顧客、または製品をデプロイしている顧客です。

### <a name="azure-marketplace"></a>Azure Marketplace

1.  顧客はオファーの "体験版" を使用しています。 体験版は、自社のビジネスを潜在的顧客と即座に共有するための、参入障壁がない、進度の速い機会です。 すべての体験版で、製品を試して詳しく知ることに関心を持ったリードが生成されます。 体験版の詳細については、[Azure Marketplace 体験版](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)に関するドキュメントをご覧ください。

    ![Marketplace の体験版の例](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. 顧客は、[今すぐ入手する] を選択した後、情報を共有することに同意します。 このリードは**初めて関心を示した**リードであり、この場合は製品を入手することに関心を示した顧客に関する情報を共有します。 リードは、取得ファンネルの最上位になります。

   ![[今すぐ入手する] オプション](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. 顧客は [Azure Portal](https://portal.azure.com/) で [購入] を選択して、製品を入手します。 このリードは**アクティブな**リードであり、この場合は製品のデプロイを始めた顧客に関する情報を共有します。

   ![[購入] オプション](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  顧客が自社のプランの「体験版」を使用しました。 体験版は、自社のビジネスを潜在的顧客と即座に共有するための、参入障壁がない、進度の速い機会です。 すべての体験版で、製品を試して詳しく知ることに関心を持ったリードが生成されます。 体験版の詳細については、[AppSource 体験版](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive)に関するブログ記事をご覧ください。

    ![体験版の例](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  顧客は、[今すぐ入手する] を選択した後、情報を共有することに同意します。 このリードは**初めて関心を示した**リードであり、この場合は製品を入手することに関心を示した顧客に関する情報を共有します。 リードは、取得ファンネルの最上位になります。

      ![[今すぐ入手する] オプション](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  顧客はオファーの [連絡する] を選択します。 このリードは**アクティブな**リードであり、この場合は製品に関するフォローアップを求めた顧客に関する情報を共有します。

    ![[連絡する] オプション](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>リードのデータ
---------

顧客獲得プロセスの間に受け取った各リードに対しては、特定のフィールドにデータが含まれます。 リードは複数のステップから得られるので、リードに対応する最良の方法は、フォローアップの重複を排除し、フォローアップをパーソナライズすることです。 これにより、顧客はそれぞれ適切なメッセージを受け取ることができ、固有の関係を築くことができます。

### <a name="lead-source"></a>リードのソース

リードのソースの形式は、"**ソース**-**アクション** |  **オファー**" です

**Sources**:"AzureMarketplace"、"AzurePortal"、"TestDrive"、"AppSource (SPZA)"

**Actions**:
- "INS" -- インストール。 このアクションは、Azure Marketplace または AppSource で顧客が製品を購入したことを示します。
- "PLT" -- パートナー主導の試用版 (Partner Led Trial) を表します。 このアクションは、AppSource で顧客が [連絡する] オプションを使用したことを示します。
- "DNC" -- 連絡不要 (Do Not Contact) を表します。 このアクションは、AppSource で、アプリ ページにクロスリストされたパートナーへの問い合わせが要求されたことを示します。 この顧客がアプリでクロスリストされたことが通知されますが、顧客に連絡する必要はありません。
- "Create" -- このアクションは、Azure Portal 内でのみ使用され、顧客がアカウントにオファーを購入すると生成されます。
- "StartTestDrive" -- このアクションは、体験版に対してのみ使用され、顧客が体験版を開始すると生成されます。

**オファー**

checkpoint.check-point-r77-10sg-byol、bitnami.openedxcypress、docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a は、発行元と特定のオファーに対して割り当てられる一意の ID の例です。


### <a name="customer-info"></a>顧客の情報

次の例のフィールドでは、リードに含まれる顧客情報が示されています。
- FirstName: John
- LastName: Smith
- Email: jsmith\@microsoft.com
- Phone: 1234567890
- Country: US
- Company: Microsoft
- タイトル:CTO

>[!Note]
>個々のリードについて、上記の例のすべてのデータが常に得られるわけではありません。

Microsoft はリードの強化に積極的に取り組んでいます。ここに記載されたデータ フィールド以外に希望のデータ フィールドがある場合は、[フィードバックをお送りください](mailto:AzureMarketOnboard@microsoft.com)。

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>CRM システムをクラウド パートナー ポータルと接続する方法
------------------------------------------------------------

リードの取得を開始するために、Cloud パートナー ポータルにリード管理コネクタを構築して、CRM 情報を簡単に組み込むことができるようにしました。接続は Microsoft が行います。 これにより、外部システムと統合するために多くのエンジニアリング作業を行わなくても、Marketplace で獲得したリードを簡単に活用できるようになります。

![リード管理コネクタ](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Microsoft はさまざまな CRM システムにリードを書き込むことができます。また、Azure ストレージ テーブルに直接書き込むこともできます。パートナーは書き込まれたリードを自由に管理できます。 以下の各リンクでは、可能性のあるリードの宛先に接続するための手順が示されています。

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md): Dynamics CRM Online を構成してリードを取得する手順がわかります。
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md): Marketo Lead Configuration を設定してリードを取得する手順がわかります。
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md): Salesforce インスタンスを設定してリードを取得する手順がわかります。
-    [Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md): Azure テーブルにリードを取得するための Azure ストレージ アカウントを設定する手順がわかります。
-   [Https エンドポイント](./cloud-partner-portal-lead-management-instructions-https.md): Https エンドポイントを設定してリードを取得する手順がわかります。

パートナーがリードの宛先を構成してオファーを発行すると、Microsoft は接続を検証し、パートナーにテスト リードを送信します。 公開前にプランを確認したときに、プレビュー環境でプランを自分で入手してみることで、リードの接続をテストすることもできます。 リードが失われないように、リード設定を最新の状態に保つことが重要です。そのため、パートナー側で変更を加えるたびに、これらの接続を必ず更新してください。

<a name="what-next"></a>次のステップ
----------

技術的な設定が完了したら、これらのリードを現在の販売/マーケティング戦略と運用プロセスに組み込む必要があります。 Microsoft は、パートナーの販売プロセス全体について理解を深めることに強い関心を持っており、パートナーを成功に導くために、パートナーと緊密に連携して質の高いリードと十分なデータを提供していきたいと考えてます。 顧客を成功に導く上で役立つ追加情報と共に送信されるリードを最適化し、強化する方法についてフィードバックをお待ちしています。 Marketplace Leads によって販売チームがより大きな成果を挙げることができるようにするための[フィードバック](mailto:AzureMarketOnboard@microsoft.com)や提案に関心がある場合は、ぜひお知らせください。
