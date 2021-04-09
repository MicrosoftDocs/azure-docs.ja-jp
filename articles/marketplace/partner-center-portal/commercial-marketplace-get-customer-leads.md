---
title: Microsoft コマーシャル マーケットプレースからのリード管理
description: Microsoft AppSource および Azure Marketplace のオファーからリードを生成し、受け取る方法について説明します
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 1d5f2248d94796d5e3ee76301642a95abddebfe4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489337"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>コマーシャル マーケットプレース オファーからの顧客リード

リードとは、[Microsoft AppSource](https://appsource.microsoft.com) および [Azure Marketplace](https://azuremarketplace.microsoft.com) からのオファーに関心を示しているか、それをデプロイしている顧客です。 オファーがコマーシャル マーケットプレースに発行された後で、リードを受け取ることができます。 この記事では、リード管理の次の概念について説明します。

* コマーシャル マーケットプレース オファーによってリードを生成して、ビジネス チャンスを逃さないようにする方法。 
* カスタマー リレーションシップ マネジメント (CRM) システムをオファーに接続して、潜在顧客を 1 か所で管理できるようにする方法。
* 連絡をくれた顧客をフォローアップできるように、Microsoft から送信されるリード データ。

## <a name="generate-customer-leads"></a>潜在顧客の生成

リードが生成される場所を次に示します。

- 顧客は、コマーシャル マーケットプレースから **[連絡する]** を選択した後、自身の情報を共有することに同意します。 このリードは、*初めて関心を示した* リードです。 製品を入手することに関心を示した顧客に関する情報が Microsoft によって共有されます。 リードは、取得ファンネルの最上位になります。

    ![Dynamics 365 の [連絡する]](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- 顧客は、 **[今すぐ入手する]** (または [Azure portal](https://portal.azure.com/)で **[作成]** を選択) します。 このリードは *アクティブ* なリードです。 製品のデプロイを始めた顧客に関する情報が Microsoft によって共有されます。

    ![SQL の [今すぐ入手する] ボタン](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server の [作成] ボタン](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- お客様は **[体験版]** または **[無料試用版]** を選択して、オファーを試します。 体験版または無料試用版は、自社のビジネスを潜在的顧客と即座に共有するための、参入障壁がない、進度の速い機会です。

    ![Dynamics 365 の [体験版] ボタン](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 の [無料試用版] ボタン](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>CRM システムに接続する

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>リード データについて

顧客獲得プロセスの間に受け取った各リードに対しては、特定のフィールドにデータが含まれます。 最初に探すフィールドは `LeadSource` フィールドで、次の形式に従います:**Source-Action** | **Offer**。

**Sources**:このフィールドの値は、リードを生成したマーケットプレースに基づいて設定されます。 指定できる値は、`"AzureMarketplace"`、`"AzurePortal"`、`"AppSource (SPZA)"` です。

**Actions**:このフィールドの値は、顧客がマーケットプレース内で行ったアクションに基づいて設定されます。このアクションにより、リードが生成されています。

次のいずれかの値になります。

- **"INS"** :*インストール* を表します。 このアクションは、Azure Marketplace または AppSource で顧客が製品を取得したことを示します。
- **"PLT"** :*パートナー主導の試用版 (Partner Led Trial)* を表します。 このアクションは、AppSource で顧客が **[連絡する]** オプションを選択したことを示します。
- **"DNC"** :*連絡不要* を表します。 このアクションは、AppSource で、アプリ ページにクロスリストされたパートナーへの問い合わせが要求されたことを示します。 この顧客がアプリでクロスリストされたことを通知しますが、顧客に連絡する必要はありません。
- **"Create"** :このアクションは、Azure portal 内でのみ使用され、顧客が自身のアカウントにオファーを購入すると生成されます。
- **"StartTestDrive"** :このアクションは、**体験版** オプションに対してのみ使用され、顧客が体験版を開始すると生成されます。

**Offers**:コマーシャル マーケットプレースには複数のプランがある場合があります。 このフィールドの値は、リードを生成したオファーに基づいて設定されます。 発行者 ID とオファー ID は両方ともこのフィールドで送信されます。これらは、オファーをマーケットプレースに発行したときに指定した値です。

次の例では、有効な形式 `publisherid.offerid` の値を示します。 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>顧客情報

顧客の情報は、複数のフィールドを介して送信されます。 次の例では、リードに含まれる顧客情報が示されています。

- FirstName: John
- LastName: Smith
- Email: jsmith\@microsoft.com
- Phone:1234567890
- 国: US
- Company: Microsoft
- タイトル:CTO

>[!NOTE]
>個々のリードについて、上記の例のすべてのデータが常に得られるわけではありません。 「潜在顧客の生成」セクションで説明したように、リードは複数のステップから得られるので、リードに対応する最良の方法は、レコードの重複を排除し、フォローアップをパーソナライズすることです。 これにより、顧客はそれぞれ適切なメッセージを受け取り、固有の関係を築くことができます。

## <a name="best-practices-for-lead-management"></a>リード管理のベスト プラクティス

販売サイクルを通じてリードを促進するためのいくつかの推奨事項を次に示します。

- **プロセス**: マイルストーン、分析、明確なチーム所有権を持つ明確な販売プロセスを定義します。
- **限定**:リードが完全に条件に合致しているかどうかを示す前提条件を定義します。 販売担当者とマーケティング担当者が完全な販売プロセスを実行する前に、リードを慎重に見込みありと評価するように確認します。
- **フォローアップ**:24 時間以内に忘れずにフォローアップしてください。 顧客が体験版をデプロイした直後に、リードを任意の CRM に追加します。リードの熱が冷めないうちにメールを送信します。 お客様の製品がリードの問題解決に役立っているかどうかをより深く理解するために、電話連絡のスケジュール設定を要求します。 一般的なトランザクションでは、多くのフォローアップの電話が必要です。
- **育成**:利益率を高めるために、リードを育成します。 様子を見ますが、やり過ぎないようにします。 リードをクローズする前に、少なくとも数回メールを送信することをお勧めします。1 回試みただけで諦めないようにしましょう。 その顧客は製品に直接関わり、無料試用版に時間を費やしたことを忘れないでください。有望な見込み客です。

技術的な設定が完了したら、これらのリードを現在の販売/マーケティング戦略と運用プロセスに組み込みます。 Microsoft は、パートナーの販売プロセス全体について理解を深めることに関心を持っており、パートナーを成功に導くために、パートナーと緊密に連携して質の高いリードと十分なデータを提供していきたいと考えてます。 顧客を成功に導く上で役立つ追加情報と共に送信されるリードを最適化し、強化する方法についてフィードバックをお待ちしています。 コマーシャル マーケットプレースのリードについて販売チームがより大きな成果を挙げることができるようにするための[フィードバック](mailto:AzureMarketOnboard@microsoft.com)や提案に関心がある場合は、ぜひお知らせください。

## <a name="next-steps"></a>次のステップ

- [リード管理に関する FAQ とトラブルシューティング](../lead-management-faq.md)