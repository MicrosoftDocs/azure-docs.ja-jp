---
title: 自主的移行ツールを使用して Azure Monitor のクラシック アラートを移行する
description: 自主的移行ツールを使用してクラシック アラート ルールを移行する方法について説明します。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631698"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>自主的移行ツールを使用してクラシック アラート ルールを移行する

[以前発表された](monitoring-classic-retirement.md)ように、Azure Monitor のクラシック アラートは 2019 年 7 月に廃止される予定です。 Azure portal では、移行を自主的にトリガーするための移行ツールが提供されており、クラシック アラート ルールを使用しているお客様に展開されつつあります。 この記事では、2019 年 7 月に自動移行が始まる前に、移行ツールを使用してクラシック アラート ルールを自主的に移行する方法を、順を追って説明します。

## <a name="benefits-of-new-alerts"></a>新しいアラートの利点

クラシック アラートは、Azure Monitor の統合された新しいアラート機能で置き換えられます。 この新しいアラート プラットフォームには以下の利点があります。

- [より多くの Azure サービス](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)に対するさまざまな多次元メトリックに関するアラート
- 新しいメトリックのアラートは[マルチリソースのアラート ルール](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)をサポートしており、多数のルールを管理するオーバーヘッドが大幅に減少します。
- 統合された通知メカニズム
  - [アクション グループ](action-groups.md)は、新しい種類のアラートすべて (メトリック、ログ、アクティビティ ログ) で機能するモジュール式の通知メカニズムです
  - SMS、音声、ITSM Connector などの新しい通知メカニズムも活用できるようになります
- [統合されたアラート エクスペリエンス](alerts-overview.md)によって、異なるシグナル (メトリック、アクティビティ ログ、ログ) に関するすべてのアラートが 1 か所にまとめられます

## <a name="before-you-migrate"></a>移行の前に

移行の一環として、クラシック アラート ルールは同等の新しいアラート ルールに変換され、アクション グループが作成されます。

- 新しいアラート ルールの作成および管理のための通知ペイロード形式と API は、より多くの機能をサポートしているため、クラシック アラート ルールのものとは異なります。 [移行のための準備を行う方法](alerts-prepare-migration.md)を学習してください。

- 一部のクラシック アラート ルールは、ツールを使用して移行できません。 [どのルールが移行不能であるかを確認し、それらを移行する方法を学習してください](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated)。

    > [!NOTE]
    > 移行プロセスは、クラシック アラート ルールの評価には影響を与えません。 それらは、移行されて新しいアラート ルールが評価を開始するまで、引き続き実行され、アラートを送信します。


## <a name="how-to-use-the-migration-tool"></a>移行ツールの使用方法

以降の手順では、Azure portal でクラシック アラート ルールの移行をトリガーする方法について説明します。

1. [Azure portal](https://portal.azure.com) で、**[モニター]** をクリックします。

2. **[アラート]** をクリックしてから、**[アラート ルールの管理]** または **[クラシック アラートの表示]** をクリックします。

3. **[Migrate to new rules]\(新しいルールに移行)** をクリックして移行ランディング ページに移動します。 このページには、すべてのサブスクリプションとそれらの移行状態の一覧が表示されます。

    ![移行-ランディング](media/alerts-migration/migration-landing.png "ルールを移行する")

4. ツールを使用して移行できるサブスクリプションはすべて、**[Ready to migrate]\(移行準備ができました)** とマークされます。

    > [!NOTE]
    > 移行ツールは、クラシック アラート ルールを使用しているすべてのサブスクリプションに対して段階的に展開中です。 展開の初期段階では、一部のサブスクリプションについては移行準備ができていないと思われる場合もあります。

5. 1 つ以上のサブスクリプションを選択し、**[Preview migration]\(移行のプレビュー)** をクリックします

6. このページでは、一度に 1 つのサブスクリプションについて、移行されるクラシック アラート ルールの詳細を参照できます。 .csv 形式で、**このサブスクリプションの移行の詳細をダウンロードする**こともできます。

    ![移行-プレビュー](media/alerts-migration/migration-preview.png "移行をプレビューする")

7. 移行状態の通知を受け取る**メール アドレス**を 1 つ以上指定します。 移行が完了したとき、またはユーザーのアクションが必要なときに、マイクロソフトからメールが送信されます。

8. **[移行の開始]** をクリックします。 確認ダイアログ ボックスに表示される情報を読み、移行プロセスを開始する準備が整っているかどうかを確認します。

    >[!IMPORTANT]
    > いったんサブスクリプションの移行プロセスを開始すると、そのサブスクリプションのクラシック アラート ルールの編集/作成はできなくなります。 ただしクラシック アラート ルールは、移行されるまでは実行され続け、ユーザーにアラートが提供されます。 これは、クラシック アラート ルールと移行中に作成された新しい規則の忠実性のことです。 サブスクリプションの移行が完了すると、クラシック アラート ルールはこれ以上使用できなくなります。

    ![移行-確認](media/alerts-migration/migration-confirm.png "移行の開始を確認する")

9. 移行が完了したとき、またはユーザーのアクションが必要なときには、手順 8 で指定したメール アドレスにメールが届きます。 ポータルの移行ランディング ページからも定期的に状態を確認できます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**サブスクリプションが [Not ready for migration]\(移行準備ができていません) と一覧表示されるのはなぜですか。**

移行ツールは、すべてのお客様に段階的に展開されつつあります。 初期段階では、サブスクリプションのほとんど、またはすべてが **[Not ready for migration]\(移行準備ができていません)** とマークされる可能性があります。 ただし、4 月中旬までに、すべてのサブスクリプションの移行準備ができるはずです。

サブスクリプションの移行準備が整ったら、サブスクリプションの所有者は、ツールを使用できることを通知するメールを受信します。 この通知を見逃さないでください。

### <a name="who-can-trigger-the-migration"></a>**移行をトリガーできるのは誰ですか。**

サブスクリプション レベルで監視共同作成者ロールを割り当てられているユーザーは、移行をトリガーできるようになります。 [移行プロセスのロール ベースのアクセス制御](alerts-understand-migration.md#who-can-trigger-the-migration)の詳細について学習してください。

### <a name="how-long-is-the-migration-going-to-take"></a>**移行の所要時間はどれくらいですか。**

ほとんどのサブスクリプションの場合、移行は通常 1 時間未満で完了します。 移行ランディング ページから、移行の進行状況を追跡できます。  このときにアラートが、クラシック アラート システムまたは新しいアラート システムのいずれかで、今までどおり実行されていることを確認してください。

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**移行中に問題が発生した場合はどうすればよいですか。**

[トラブルシューティング ガイドに従って、移行中に発生する可能性がある問題の修復手順を確認](alerts-understand-migration.md#common-issues-and-remediations)してください。 移行を完了するためにユーザーの何らかのアクションが必要な場合は、移行時に指定したメール アドレスに通知が送信されます。

## <a name="next-steps"></a>次の手順

- [移行を準備する](alerts-prepare-migration.md)
- [移行ツールの動作の理解](alerts-understand-migration.md)
