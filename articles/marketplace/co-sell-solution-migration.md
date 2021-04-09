---
title: OCP GTM から Microsoft AppSource のパートナー センターへ共同販売ソリューションを移行する
description: OCP GTM から Microsoft AppSource のパートナー センターへ共同販売ソリューションを移行する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/04/2021
ms.openlocfilehash: 7ffb5a3dfd23a1515c6d21784b82ccdbf0674f0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593409"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>OCP GTM からコマーシャル マーケットプレースへの共同販売ソリューションの移行

Microsoft は、発行エクスペリエンスを移動します。 [コマーシャル マーケットプレース](overview.md)では、オファーの作成と管理を、お客様が既に Microsoft との関係を管理しているパートナー センターに一元化することによって、Microsoft の 3 つのチャネルを通じて共同販売するための簡素化されたオファー発行を提供します。

コマーシャル マーケットプレースに登録されている Microsoft パートナーは、次のことを実行できます。

- Microsoft Direct の顧客、パートナー、販売者の各チャネルにわたってオファーを一元的に公開し、共同販売を行う。
- オファーの機能に合致する何百万人ものクラウド顧客に見てもらえるように、オファーが正しいオンライン ストア ([Microsoft AppSource](https://appsource.microsoft.com) または [Azure Marketplace](https://azure.microsoft.com)) にあることを確認する。
- ビジネス目標に合致したオファーで共同販売を行うために独自の発行エクスペリエンスを推進する。
- 既に Microsoft の関係と共同販売機会を管理しているパートナー センター内でオファーの発行を調整する。
- [Marketplace 報奨](partner-center-portal/marketplace-rewards.md)のロックを解除する。

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Microsoft との共同販売を継続するための前提条件

Microsoft Partner Network のアクティブなメンバーシップを持っており、パートナー センターのコマーシャル マーケットプレースに登録していることを確認します。

- [無料](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)で Microsoft Partner Network に参加します。 パートナーは、ビジネスを成長させるために専用のリソース、プログラム、ツール、および接続にアクセスできます。
- コマーシャル マーケットプレースにアカウントをお持ちでない場合はMicrosoft との共同販売を継続し、完全な発行エクスペリエンスにアクセスするために、[今すぐご登録](partner-center-portal/create-account.md)ください。

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Co-Sell-Ready 状態を達成するための発行の更新

お客様のソリューションを Microsoft の販売者やパートナーが検出できるようにするには、[Co-Sell Ready (共同販売準備完了) の要件](marketplace-co-sell.md)を満たす必要があります。 Microsoft 販売者がインセンティブを受け取るためには、ソリューションが、[Incentive-Eligible (インセンティブ対象) 要件](marketplace-co-sell.md)を満たしている必要があります。 パートナー センターの [共同販売] タブでこれらの要件を完了します (この記事で後述する[こちらのイメージ](#cosell-tab)を参照してください)。

> [!NOTE]
> コマーシャル マーケットプレースでは、ソリューションは発行エクスペリエンス全体で「オファー」と呼ばれています。

コマーシャル マーケットプレースに登録したら、OCP GTM からソリューションを移行する準備をします。

ソリューションを OCP GTM からインポートする前に、こちらの手順を実行してください。

1. 会社の[発行元リスト](https://partner.microsoft.com/dashboard/account/v3/publishers/list)にアクセスします。 これには、発行アクセス権を持つアカウント所有者、マネージャー、および開発者が含まれています。 パートナー センターのユーザー ロールの詳細については、[こちら](./partner-center-portal/manage-account.md#define-user-roles-and-permissions)を参照してください。
2. リストに記載されているいずれかの連絡先に連絡し、"*マネージャー*" または "*開発者*" としてコマーシャル マーケットプレースに [ユーザーを追加](https://partner.microsoft.com/dashboard/account/usermanagement)するように依頼します。これは、ソリューションを編集したり発行したりできるのはこれらのロールだけだからです。
3. 開発者と協力して、ソリューションを OCP GTM アカウントからコマーシャル マーケットプレースに移動します。
4. 次のうちどれを実行するかを決定します。
    1. このソリューションを、コマーシャル マーケットプレースにある同様のオファーにマージする。
    1. このソリューションを OCP GTM からコマーシャル マーケットプレースに移行する。
    1. このソリューションを破棄する。

## <a name="migrate-your-solutions-from-ocp-gtm"></a>OCP GTM からソリューションを移行する

1. [こちら](https://partner.microsoft.com/solutions/migration#)から移行を開始します。
2. **[概要]** ページを選択し、**こちらをクリックして開始します**。

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="パートナー センターの [概要] ページ、[概要] タブ。":::

3. 移行を開始するには、 **[ソリューション]** タブを選択します。これにより、MPN ID に関連付けられているすべてのソリューションが表示されます。

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="パートナー センターの [概要] ページ、[ソリューション] タブ。":::

    > [!NOTE]
    > コマーシャル マーケットプレースへの移行を保留中のソリューションがない場合は、このタブに記載されます。 Microsoft との共同販売を継続するには、移行したソリューションがコマーシャル マーケットプレースに発行されていることを確認します。

    ソリューションの状態の詳細については、ツール ヒントを確認してください。 アクションを保留中のすべてのソリューションは、**アクション** の下に一覧表示されています。<a name="beginmigration"></a>

4. 移行するソリューションに対して **[移行の開始]** (上の図を参照) を選択してから、次のいずれかのオプションを選択します。

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="移行のための 3 つのオプション。":::

### <a name="merge-solution-with-a-similar-offer"></a>ソリューションを同様のオファーにマージする

このソリューションがコマーシャル マーケットプレースで既に発行されており、その 2 つを 1 つのオファーにマージする必要がある場合は、このオプションを選択します。 これにより、重複するオファーの作成が回避されます。

1. 既存のオファーを特定します。
    1. **[I want to merge this solution with a similar offer in the commercial marketplace]\(このソリューションをコマーシャル マーケットプレース内の同様のオファーにマージする\)** を選択します ([上記](#beginmigration)の **[Action required]\(アクションが必要\)** イメージを参照)。
    1. **[アクション 1]** タブには、OCP GTM ソリューションを関連付けることができるライブ コマーシャル マーケットプレース オファーが表示されています。 ライブ オファーがある場合は、一覧からそれを選択します。 選択するオファーの一覧がない場合は、Microsoft AppSource または Azure Marketplace から顧客向けのアドレス (URL) を入力します。
        [![マージ プロセスの [アクション1] タブ。](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. **[続行]** をクリックします。
1. マージを要求します。
    1. **[アクション 2]** タブには、OCP GTM ソリューションと、特定したものとのマージを要求するための手順が表示されます。 マージを要求するには、 **[Save & Contact support]\(保存してサポートに連絡\)** を選択します。これにより、ブラウザーでパートナーのサポート ページが開きます。
    1. **[問題の詳細を指定]** を選択し、次を入力します。    [![マージ プロセスの [アクション2] タブ。](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. **[Submit]\(送信\)** をクリックします。 パートナー サポート チームが 2 営業日以内にお客様に連絡します。
    1. パートナー サポートはお客様と協力して、このオファーが正常にマージされ、ライブ オファーとして発行されるようにします。

### <a name="migrate-this-solution-from-ocp-gtm"></a>このソリューションを OCP GTM から移行する

対応するオファーがまだコマーシャル マーケットプレースで発行されていない OCP GTM ソリューションがある可能性がある場合は、このオプションを選択します。 Microsoft との共同販売を継続するには、このソリューションをコマーシャル マーケットプレースに発行する必要があります。このソリューションを移行すると、OCP GTM の情報と部品表を保持することによって時間を節約できます。 このオプションでは、オファーの種類を選択する必要があります。

1. **[I want to migrate this solution from OCP GTM to the commercial marketplace]\(このソリューションを OCP GTM からコマーシャル マーケットプレースに移行する\)** ([上記](#beginmigration)の **[Action required]\(アクションが必要\)** イメージを参照) を選択し、 **[続行]** を選択します。
1. **[アクション 1]** タブで [オファーの種類](publisher-guide-by-offer-type.md)を選択してから、 **[続行]** を選択します。

    [![移行プロセスの [アクション 1] タブ。](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. **[アクション 2]** タブで、表示された一覧から [発行元プロファイル](partner-center-portal/create-account.md)を選択します。 発行元アカウントがない場合は、[パートナー センター](https://partner.microsoft.com/solutions/migration)で作成し、こちらで選択します。

    [![移行プロセスの [アクション 2] タブ。](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. ソリューションをドラフトとしてコマーシャル マーケットプレースに移行するには、 **[Create a draft offer]\(ドラフト オファーの作成\)** を選択します。 まだライブにはなりません。
1. パートナー センターで発行プロセスを続行します。 パートナー センターでの発行の詳細については、下の「[オファーをコマーシャル マーケットプレースで公開する](#make-your-offer-live-in-the-marketplace)」を参照してください。

### <a name="discard-this-solution"></a>このソリューションを破棄する

OCP GTM ソリューション内のソリューションがもう関連しなくなった場合は、このオプションを選択します。 破棄を確認するように求められます。後で取り消すこともできます。

1. **[I want to discard this solution]\(このソリューションを破棄する\)** ([上記](#beginmigration)の **[Action required]\(アクションが必要\)** イメージを参照) を選択し、 **[続行]** を選択します。
2. **[破棄]** を選択します。

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="破棄を確認します。":::

3. 破棄を元に戻すには、 **[Undo Discard]\(破棄を元に戻す\)** を選択します。

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="[Undo Discard]\(破棄を元に戻す\) リンク。":::

4. 追加のヘルプが必要な場合は、 **[ヘルプの取得]** タブを選択して、パートナー サポート チームに連絡してください。

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="[Get Help]\(ヘルプの取得\) タブのサポート リンク。":::

## <a name="make-your-offer-live-in-the-marketplace"></a>オファーをマーケットプレースで公開する

オファーをコマーシャル マーケットプレースに移行することを選択した場合は、ドラフトとして届きます。 コマーシャル マーケットプレースで公開するには、まだオファーの発行が必要です。これによって、共同販売の状態、インセンティブ、および紹介パイプラインが保持されます。

オファーを発行する前に指定する必要がある情報の詳細な指示については、[発行に関するガイド](publisher-guide-by-offer-type.md)のページを参照してください。 概要については、以下を参照してください。

1. パートナー センターの **[概要]** ページで、ドラフト オファー名を選択します。

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="パートナー センター内のオファーの概要ページ。":::

<a name="cosell-tab"></a>

2. 各タブに必要な情報を入力します。必要な場合、クラウド ソリューション プロバイダー (CSP) プログラムを通じて再販するには、(下記の左側ナビゲーション メニューにある) **[CSP を通じた再販]** ページに入力します。 要件と詳細については、 **[Learn more]\(詳細情報\)** リンクとヒントを参照してください。

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="パートナー センター内にあるオファーの概要設定のタブ。":::

3. Microsoft 販売者向けの詳細情報の一部が、OCP GTM ソリューションからコピーされました。 **[Microsoft との共同販売]** タブで残りの必要情報を入力して、オファーを共同販売できるようにします。 完了したら、 **[レビューと公開]** を選択します。 詳細については、「[コマーシャル マーケットプレース プランの共同販売を構成する](commercial-marketplace-co-sell.md)」を参照してください。

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="パートナー センター内にあるオファーの共同販売のタブ。":::

4. 送信されたすべての情報を確認したら、 **[Publish]\(発行\)** を選択して、認定レビューのためにドラフト オファーを送信します。

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="パートナー センター内にあるオファーの確認タブと [Publish]\(発行\) ボタン。":::

5. **[概要]** タブで、送信の状態を追跡します。

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="パートナー センター内の [概要] タブにあるオファーの発行状態バー。":::

6. 認定レビューが完了すると、通知されます。 アクション可能なフィードバックが提供されている場合は、それに対処してから、 **[Publish]\(発行\)** を選択して再認定を開始します。
7. オファーが認定に合格したら、提供されているリンクを使用してオファーをプレビューし、必要に応じて最終的な調整を行います。 準備ができたら、 **[一般公開する]** (上のボタンを参照) を選択して、関連するコマーシャル マーケットプレースのストアフロントにオファーを発行します。 公開されると、オファーで元の OCP GTM ソリューションからの共同販売の状態が保持されます。

## <a name="next-steps"></a>次のステップ

- [CSP パートナーによる再販](cloud-solution-providers.md)
- [コマーシャル マーケットプレース プランの共同販売を構成する](commercial-marketplace-co-sell.md)
- これらの [FAQ](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF) を表示する
