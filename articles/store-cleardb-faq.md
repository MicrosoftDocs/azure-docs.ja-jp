---
title: Azure App Service での ClearDB MySql データベースの使用に関してよく寄せられる質問 (FAQ)
description: Azure App Service での ClearDB MySql データベースの使用に関してよく寄せられる質問に答えます。
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: references_regions
ms.openlocfilehash: d9d4ae746c192e8b4afd1781c1e072fc0dc70422
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98599791"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Azure App Service での ClearDB MySql データベースの使用に関してよく寄せられる質問 (FAQ)
この FAQ では、Azure Web Apps での ClearDB MySQL データベースの使用と購入に関してよく寄せられる質問に答えます。

> [!IMPORTANT]
> 2018 年 6 月 13 日より、ClearDB は、現在 Microsoft から請求を受けている Azure ベースの顧客を ClearDB による直接請求モデルに移行しました。 この記事内の情報は古くなっています。 ClearDB データベースの作成、または Azure で作成された ClearDB データベースのアップグレードはできなくなります。
>
> 詳細および次のステップについては、「[Changes to ClearDB service plans](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/)」を参照してください。

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Azure で使用できる MySQL にはどのようなオプションがありますか。
そのサービスの最新情報については、[ClearDB](https://w2.cleardb.net/) のページを参照してください。 ClearDB は、ユーザーに代わって MySQL インフラストラクチャを管理する、MySQL のホスティング サービスです。 

Azure で MySQL をホストするには、他にもいくつかの選択肢があります。
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [Azure VM で実行する MySQL クラスター](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Azure VM で実行する MySQL の 1 つのインスタンス](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Azure Marketplace で Web アプリケーションと MySQL テンプレートを入手するには、クレジット カードが必要ですか。
ご利用のサブスクリプションの種類によります。 一般的なサブスクリプションの種類をいくつか示します。

* [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/): クレジット カードが必要です。有料の MySQL データベースの購入時にクレジット カードに課金されます。
* [無料評価版](https://azure.microsoft.com/pricing/free-trial/): Microsoft Azure サービスに使用できるクレジットが含まれていますが、サードパーティのリソースは購入できません。 サード パーティのサービスや有料の MySQL データベースを購入するには、クレジット カードを有効にしたサブスクリプションを使う必要があります。 Web Apps では無料の ClearDB MySQL データベースを作成できます。
* [MSDN サブスクリプション](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) と **MSDN 向け開発テスト用の従量課金制プラン**: 無料評価版と同様に、MSDN サブスクリプションで ClearDB から有料の MySQL ソリューションを購入するには、クレジット カードが必要です。
* [エンタープライズ契約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA のお客様の場合、Azure Marketplace (サード パーティ) での購入はすべて EA に対して課金されます (四半期ごとに個別の請求書が発行されます)。 マーケットプレースでの購入はすべて、Azure 前払い (旧称: 年額コミットメント) とは別に課金されます。 現時点では、アゼルバイジャン、クロアチア、ノルウェー、プエルトリコで登録されたお客様は Azure ストアをご利用いただけませんのでご注意ください。 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Azure Marketplace から Web アプリケーションと MySQL に 3.50 ドルを課金されているのはなぜですか。
既定のデータベース オプションは Titan で、価格は 3.50 ドルです。 データベースの作成時には価格が表示されないため、このデータベースを誤って購入してしまった可能性があります。 今後プロセスの改善に努めてまいりますが、それまでは、Web アプリケーションとデータベースについて選択した価格レベルをすべて確認してから、 **[作成]** をクリックしてリソースのデプロイを開始してください。

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>自分が所有する Azure 仮想マシンで MySQL を実行しています。 自分の Azure Web アプリケーションを自分のデータベースに接続することはできますか。
はい。 Azure VM にご自身の Web アプリケーションへのリモート アクセス権が付与されていれば、そのアプリケーションをご自身のデータベースに接続していただけます。 詳細については、「 [Windows Server 2012 R2 を実行するクラシック デプロイ モデルで作成された仮想マシンに MySQL をインストールする](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>ClearDB Premium MySQL クラスターがサポートされている国や地域を教えてください。
ClearDB Premium MySQL クラスターは、インド、オーストラリア、ブラジル南部、中国を除く、全世界のすべての Azure リージョンでご利用いただけます。

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>ClearDB Premium クラスター ソリューションでデータベースを作成する前に新しいクラスターを作成できますか。
いいえ。空の ClearDB クラスターの作成はサポートされていません。 Azure Portal ではクラスター内にデータベースを作成できるので、データベースの作成と同時に新しいクラスターが作成されることはあります。

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>アプリケーションが使用中の ClearDB MySQL データベースを削除しようとすると、警告されますか。
いいえ。アプリケーションが使用中の Marketplace での購入物を削除しようとしても、警告は表示されません。

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>ClearDB データベースを作成できるリージョンを教えてください。
Azure Marketplace は、アゼルバイジャン、クロアチア、ノルウェー、プエルトリコで登録されたお客様にはご利用いただけません。 これらのリージョンでは、ClearDB を使用できません。

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>運用中の Web アプリケーションとデータベースにはどの価格レベルを選択すればよいですか。
Web Apps には Basic 以上の価格レベルを使用してください。 ClearDB には Saturn か Jupiter をお勧めします。 [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) と [ClearDB MySQL データベース](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/)の両方について、各価格レベルの機能と制限事項を確認し、ニーズに合ったレベルを選んでください。

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>ClearDB データベースのプランをアップグレードする方法を教えてください。
[Azure Portal](https://portal.azure.com) では、ClearDB の共有ホスティング データベースをスケールアップできます。 詳しくは、[こちらの記事](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/)をご覧ください。 現在、Azure Portal での ClearDB Premium クラスターへのアップグレードはサポートされていません。

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Azure Portal に ClearDB データベースが表示されないのはなぜですか。
クラシックで ClearDB データベースを作成した場合、そのデータベースは [Azure Portal](https://portal.azure.com) に表示されません。 このシナリオの回避策はありません。

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>データベースがダウンしたときはどこに問い合わせればよいですか。
データベース関連の問題については、 [ClearDB のサポート](https://www.cleardb.com/developers/help/support) にお問い合わせください。 その際、Azure サブスクリプションの情報を提供できるよう準備しておいてください。

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>ClearDB MySQL データベース クラスター ソリューションに追加ユーザーを作成できますか。
いいえ。 追加ユーザーを作成することはできません。ただし、ClearDB データベース クラスターに追加のデータベースを作成することはできます。  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Basic/Pro シリーズのデータベースを、Planetary プランと同様、ClearDB ポータルですぐにインプレース アップグレードできますか。
はい、Basic シリーズのデータベースはインプレース アップグレードできます (Basic 60 ～ Basic 500)。 Pro シリーズも、Pro 60 を除き、インプレース アップグレードできます (Pro 125 ～ Pro 1000)。 現在、Pro 60 データベースのアップグレードはサポートされていません。 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>サブスクリプションから別のサブスクリプションにリソースを移行すると、ClearDB MySQL データベースも移行されますか。
サブスクリプション間でリソースの移行を実行する場合、いくつかの [制限事項](azure-resource-manager/management/move-support-resources.md) が適用されます。 ClearDB MySQL データベースは、サード パーティのサービスであるため、Azure サブスクリプションの移行時にこのデータベースは移行されません。 Azure リソースを移行する前に MySQL データベースの移行に対処していない場合、ClearDB MySQL データベースが無効化される可能性があります。 事前に手動でデータベースを移行してから、Web アプリの Azure サブスクリプションを移行してください。 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>サブスクリプションで使用制限に達しました。 制限を削除し、App Service はオンラインになっていますが、データベースにアクセスできません。 ClearDB データベースを再有効化するには、どうしたらいいですか。
データベースを再有効化するには、[ClearDB サポート](https://www.cleardb.com/developers/help/support)にお問い合わせください。 その際、Azure サブスクリプションの情報とデータベース名をサポートに提供してください。

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>ClearDB データベースのサブスクリプションをクレジット カードから EA に移行できますか。
既存の ClearDB データベースには、既存のサブスクリプションに関連付けられているクレジット カードが引き続き使われます。 EA サブスクリプションを使用するには、次の方法で新しいデータベースにデータを移行する必要があります。

* EA サブスクリプションで新しい ClearDB データベースを購入します。
* 新しいデータベースにデータを移行します。
* 新しいデータベースを使用するようにアプリケーションを更新します。
* 既存の ClearDB データベースを削除します。

MySQL (ClearDB) を使用して新しい Web アプリケーションを作成するか、MySQL データベース (ClearDB) を作成する場合、選択したサブスクリプションによってサービスの支払い方法が変わります。 EA サブスクリプションの場合は、Azure Portal で ClearDB などのサード パーティ サービスを調達することができます。 EA サブスクリプションは Azure 前払いとは別に課金され、四半期ごとに未払い分が請求されます。 EA のお客様は、サード パーティの Marketplace サービスの支払いのために、クレジット カードなどの支払方法を設定する必要があります。

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>EA サブスクリプションでの ClearDB リソースの料金はどこで確認できますか。
ダイレクト EA のお客様の場合、Azure Marketplace の料金はエンタープライズ ポータルに表示されます。 なお、マーケットプレースでの購入および使用はすべて Azure 前払いとは別に課金され、四半期ごとに未払い分が請求されます。 EA のお客様は、サード パーティのサービス プロバイダーに直接お支払いいただく必要があります。そのためには、EA アカウントでクレジット カードなどの支払方法を有効にします。

インダイレクト EA のお客様の場合は、エンタープライズ ポータルの **[サブスクリプションの管理]** ページで Azure Marketplace のサブスクリプションを確認できますが、価格は非表示になっています。 Marketplace の料金については、LSP にお問い合わせください。

EA Azure の登録管理者は、Azure Marketplace のサード パーティ サービスへのアクセスを管理することができます。 エンタープライズ ポータルの [アカウント] セクションの下にある [アカウントとサブスクリプションの管理] で、ストアでのサード パーティ サービスの購入を無効または再度有効にします。

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>EA サブスクリプションでの ClearDB サービスの料金については、どこに問い合わせればよいですか。
EA 登録での料金に関するご質問は、 [エンタープライズ カスタマー サポート](https://aka.ms/AzureEntSupport) にお問い合わせください。 EA ポータル サポート チームがご質問に回答し、問題解決のお手伝いをします。

## <a name="more-information"></a>詳細情報
[Azure Marketplace FAQ](https://azure.microsoft.com/marketplace/faq/)