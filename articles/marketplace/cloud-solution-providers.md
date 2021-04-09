---
title: クラウド ソリューション プロバイダー - Microsoft コマーシャル マーケットプレース
description: コマーシャル マーケットプレース内の Microsoft クラウド ソリューション プロバイダー (CSP) プログラムのパートナー チャネルを通じてオファーを販売する方法を説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: c906d37a01f0fca2d4114e8ba07078fc46eec88b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93131175"
---
# <a name="cloud-solution-provider-program"></a>クラウド ソリューション プロバイダー プログラム

この記事では、クラウド ソリューション プロバイダー (CSP) プログラムから提供できるよう、オファーを構成する方法について説明します。 オファーを[コマーシャル マーケットプレースのオンライン ストア](overview.md#commercial-marketplace-online-stores)で公開するだけでなく、CSP プログラムを通じて販売することにより、このプログラムを利用している数百万の Microsoft の正規のお客様にオファーを提供できます。

新規または既存のオファーを CSP プログラムで提供できるよう、オプトイン方式で構成できます。これにより、CSP パートナーは、貴社の製品を販売したり、顧客向けのバンドル ソリューションを作成したりできるようになります。

発行元は、エンド ユーザーへの Break-Fix サポートと、CSP プログラムのパートナーまたは顧客、あるいはその両方がサポートを得るために連絡するための仕組みを提供する責任があります。 ユーザー ドキュメント、トレーニング、サービスの正常性/停止通知 (該当する場合) を CSP プログラムのパートナーに提供して、CSP プログラムのパートナーが顧客からのレベル 1 のサポートを処理する体制を整えることができるようにすることをお勧めします。  

次のオファーは、CSP プログラムのパートナーによる販売対象として選択できます。

- サービスとしてのソフトウェア (SaaS) トランザクション オファー
- 仮想マシン (VM)
- ソリューション テンプレート
- マネージド アプリケーション

> [!NOTE]
> コンテナーとライセンス持ち込み (BYOL) VM プランは、既定では、CSP プログラムのパートナーによる販売対象として選択されています。

## <a name="how-to-configure-an-offer"></a>オファーの構成方法

パートナー センターでオファーを作成するときに、CSP プログラムのオプトイン設定を構成します。 [発行元エクスペリエンスの変更に関する詳細](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)。

### <a name="partner-center-opt-in"></a>パートナー センターのオプトイン

オプトイン エクスペリエンスは、CSP リセラーの対象ユーザー モジュールの下にあります。

![CSP リセラーの対象](media/marketplace-publishers-guide/csp-reseller-audience.png)

次の 3 つのオプションから選択できます。

1. CSP プログラムの任意のパートナー。
2. 選択した CSP プログラムの特定のパートナー。
3. CSP プログラムでパートナーはいません。

#### <a name="option-1-any-partner-in-the-csp-program"></a>オプション 1: CSP プログラムの任意のパートナー

![CSP プログラムの任意のパートナー](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 このオプションを選択すると、自分のオファーを CSP プログラムのすべてのパートナーが顧客に再販できるようになります。

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>オプション 2:選択した CSP プログラムの特定のパートナー

![選択した CSP プログラムの特定のパートナー](media/marketplace-publishers-guide/csp-reseller-option-two.png)

このオプションを選択すると、自分のオファーを再販できる CSP プログラムのパートナーを承認できます。

パートナーを認可するには、 **[CSP パートナーの選択]** を選択してメニューを表示し、パートナー名または CSP Azure Active Directory (Azure AD) テナント ID で検索します。

![CSP の選択メニュー](media/marketplace-publishers-guide/csp-pop-up-module.png)

**国**、**コンピテンシー**、**スキル** などの検索フィルターを適用できます。

![パートナー検索に対する国/地域、コンピテンシー、スキルのフィルター](media/marketplace-publishers-guide/csp-add-resellers.png)

パートナーの一覧を選択したら、 **[追加]** を選択します。

![CSP プログラムで承認されたパートナーの一覧の例](media/marketplace-publishers-guide/csp-add-resellers-details.png)

選択したパートナーの一覧を示す表が、CSP リセラーの対象ページに表示されます。

![CSP リセラーの対象ページのパートナー一覧の表](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

**[下書きの保存]** を選択して、変更を登録します。

このオファーが発行されていない場合は、オファーを発行して、選択したパートナーが使用できるようにする必要があります。

>[!NOTE]
>特定のリージョンの CSP プログラムのパートナーを承認すると、そのパートナーはその特定のリージョンに属する顧客に対してオファーを販売できます。 リージョンでの CSP オファーの分類方法について詳しくは、[クラウド ソリューション プロバイダー プログラムのリージョン別の市場と通貨](/partner-center/regional-authorization-overview)に関するページをご覧ください。

既に発行されているオファーの CSP の一覧を更新する場合は、新しいパートナーを追加し、 **[Sync CSP audience]\(CSP の対象を同期する\)** を選択します。

承認されたパートナーの一覧が既に作成されているオファーがあり、別のオファーで同じ一覧を使用する場合は、**インポート/エクスポート** を使用します。 CSP の一覧があるオファーに移動し、 **[CSP のエクスポート]** を選択します。 機能によって作成された .csv ファイルを、別のオファーにインポートできます。

#### <a name="option-3-no-partners-in-the-csp-program"></a>オプション 3:CSP プログラムでパートナーはいません

![CSP プログラムでパートナーはいません](media/marketplace-publishers-guide/csp-reseller-option-three.png)

このオプションを選択すると、そのオファーは CSP プログラムからオプトアウトされます。 この選択はいつでも変更できます。

## <a name="deauthorize-partners-in-the-csp-program"></a>CSP プログラムのパートナーの承認を取り消す

CSP プログラムのパートナーを承認し、そのパートナーが既に製品を顧客に再販している場合、そのパートナーの承認を取り消すことはできません。

CSP プログラムのパートナーが製品を顧客に販売していない場合に、オファーを発行した後で CSP を削除するには、次の手順のようにします。

1. [サポート リクエスト ページ](https://aka.ms/marketplacepublishersupport)に移動します。 最初のいくつかのドロップダウン メニューは、自動的に入力されます。

   > [!NOTE]
   > この事前設定されたドロップダウン メニューの選択は変更しないでください。

2. **[Select the product version]\(製品バージョンの選択\)** で、 **[Live offer management]\(ライブ オファー管理\)** を選択します。
3. **[Select a category that best describe the issue]\(問題の説明として最も適切なカテゴリを選択してください\)** で、オファーを示すカテゴリを選択します。
4. **[Select a problem that best describes the issue]\(問題の説明として最も適切な問題を選択してください\)** で、 **[Update existing offer]\(既存のオファーの更新\)** を選択します。
5. **[次へ]** を選択して **問題の詳細ページ** に移動し、問題の詳細を入力します。
6. 問題のタイトルを "**Deauthorize CSP**" (CSP の承認を取り消す) とし、残りの必要なセクションを入力します。

## <a name="navigate-between-options"></a>オプション間の移動

3 つの CSP リセラー オプションの間を移動するには、このセクションを使用します。

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>移動元がオプション 1: CSP プログラムの任意のパートナー

現在のオファーが **オプション 1: CSP プログラムの任意のパートナー** であり、他の 2 つのオプションのいずれかに移動する場合は、次の手順に従ってリクエストを作成します。

1. [サポート リクエスト ページ](https://aka.ms/marketplacepublishersupport)に移動します。 最初のいくつかのドロップダウン メニューは、自動的に入力されます。

   > [!NOTE]
   > この事前設定されたドロップダウン メニューの選択は変更しないでください。

2. **[Select the product version]\(製品バージョンの選択\)** で、 **[Live offer management]\(ライブ オファー管理\)** を選択します。
3. **[Select a category that best describe the issue]\(問題の説明として最も適切なカテゴリを選択してください\)** で、オファーを示すカテゴリを選択します。
4. **[Select a problem that best describes the issue]\(問題の説明として最も適切な問題を選択してください\)** で、 **[Update existing offer]\(既存のオファーの更新\)** を選択します。
5. **[次へ]** を選択して **問題の詳細ページ** に移動し、問題の詳細を入力します。
6. 問題のタイトルを "**Deauthorize CSP**" (CSP の承認を取り消す) とし、残りの必要なセクションを入力します。

> [!NOTE]
> オプション 2 に移動しようとしていて、CSP プログラムのパートナーが既に顧客にオファーを再販している場合、そのパートナーは既定で既に承認済みパートナーの一覧に登録されています。  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>移動元がオプション 2: 選択した CSP プログラムの特定のパートナー

現在のオファーが **オプション 2: 選択した CSP プログラムの特定のパートナー** で、移動先が **オプション 1: CSP プログラムの任意のパートナー** である場合は、次の手順を使用してリクエストを作成します。

1. [サポート リクエスト ページ](https://aka.ms/marketplacepublishersupport)に移動します。 最初のいくつかのドロップダウン メニューは、自動的に入力されます。

   > [!NOTE]
   > この事前設定されたドロップダウン メニューの選択は変更しないでください。

2. **[Select the product version]\(製品バージョンの選択\)** で、 **[Live offer management]\(ライブ オファー管理\)** を選択します。
3. **[Select a category that best describe the issue]\(問題の説明として最も適切なカテゴリを選択してください\)** で、オファーを示すカテゴリを選択します。
4. **[Select a problem that best describes the issue]\(問題の説明として最も適切な問題を選択してください\)** で、 **[Update existing offer]\(既存のオファーの更新\)** を選択します。
5. **[次へ]** を選択して **問題の詳細ページ** に移動し、問題の詳細を入力します。
6. 問題のタイトルを "**Deauthorize CSP**" (CSP の承認を取り消す) とし、残りの必要なセクションを入力します。

 現在のオファーが **オプション 2: 選択した CSP プログラムの特定のパートナー** で、移動先が **オプション 3: CSP プログラムでパートナーはいません** である場合は、以前に承認した CSP プログラムのパートナーが最終顧客にオファーを再販していない場合にのみ、そのオプションに移動できます。 次の手順を使用してリクエストを作成します。

1. [サポート リクエスト ページ](https://aka.ms/marketplacepublishersupport)に移動します。 最初のいくつかのドロップダウン メニューは、自動的に入力されます。

   > [!NOTE]
   > この事前設定されたドロップダウン メニューの選択は変更しないでください。

2. **[Select the product version]\(製品バージョンの選択\)** で、 **[Live offer management]\(ライブ オファー管理\)** を選択します。
3. **[Select a category that best describe the issue]\(問題の説明として最も適切なカテゴリを選択してください\)** で、オファーを示すカテゴリを選択します。
4. **[Select a problem that best describes the issue]\(問題の説明として最も適切な問題を選択してください\)** で、 **[Update existing offer]\(既存のオファーの更新\)** を選択します。
5. **[次へ]** を選択して **問題の詳細ページ** に移動し、問題の詳細を入力します。
6. 問題のタイトルを "**Deauthorize CSP**" (CSP の承認を取り消す) とし、残りの必要なセクションを入力します。

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>移動元がオプション 3: CSP プログラムでパートナーはいません

現在のオファーが **オプション 3: CSP プログラムでパートナーはいません** である場合は、いつでも他の 2 つのオプションのいずれかに移動できます。

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>販売とサポートの資料を CSP プログラムのパートナーと共有する

クラウド ソリューション プロバイダー プログラムのパートナーがあなたのオファーを最も効果的に表現し、あなたの組織と連携できるようにするには、リセラーが使用できる販売およびサポートの資料を送信する必要があります。 これらのリソースは、マーケットプレースのオンライン ストアの顧客には公開されません。

### <a name="partner-center-csp-channel"></a>パートナー センターの CSP チャネル

パートナー センターで CSP チャネルにオプトインしている場合、発行元は、関連するマーケティング資料がホストされている URL と、チャネルの連絡先情報を、オファー一覧モジュールで入力する必要があります。

![パートナー センターでの CSP 関連情報](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>次のステップ

- [市場投入サービス](https://partner.microsoft.com/reach-customers/gtm)の詳細を確認します。
- [パートナー センター](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)にサインインして、オファーを作成して構成します。