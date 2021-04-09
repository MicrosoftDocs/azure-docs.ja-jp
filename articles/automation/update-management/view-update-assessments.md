---
title: Azure Automation 更新プログラムの評価を表示する
description: この記事では、Update Management のデプロイに関して、更新プログラムの評価を表示する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 09/17/2020
ms.topic: conceptual
ms.openlocfilehash: 39df5888a330a92ae043e34c3043da5b1f566345
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221683"
---
# <a name="view-update-assessments-in-update-management"></a>Update Management で更新プログラムの評価を表示する

Update Management には、ご利用のマシン、不足している更新プログラム、更新プログラムのデプロイ、およびスケジュールされている更新プログラムのデプロイに関する情報が表示されます。 選択された Arc 対応サーバーから、または構成されているすべてのマシンとサーバーにおける Automation アカウントから、選択した Azure 仮想マシンにスコープを指定して評価情報を表示できます。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com)

## <a name="view-update-assessment"></a>更新の評価を確認する

Azure VM からの更新プログラムの評価を表示するには、 **[仮想マシン]** に移動し、一覧からお使いの仮想マシンを選択します。 左側のメニューで、 **[ゲスト + ホストの更新プログラム]** を選択し、 **[ゲスト + ホストの更新プログラム]** ページの **[Go to Update Management]\(Update Management に移動\)** を選択します。

Update Management には、ご利用のマシン、不足している更新プログラム、更新プログラムのデプロイ、スケジュールされている更新プログラムのデプロイに関する情報が表示されます。

[ ![Azure VM の Update Management 評価ビュー](./media/view-update-assessments/update-assessment-azure-vm.png)](./media/view-update-assessments/update-assessment-azure-vm-expanded.png#lightbox)

Arc 対応サーバーからの更新プログラムの評価を表示するには、 **[サーバー - Azure Arc]** に移動し、一覧からお使いのサーバーを選択します。 左側のメニューで、 **[ゲスト + ホストの更新プログラム]** を選択します。 **[ゲスト + ホストの更新プログラム]** ページで、 **[Go to Update Management]\(Update Management に移動\)** を選択します。

Update Management には、ご利用の Arc 対応マシン、不足している更新プログラム、更新プログラムのデプロイ、スケジュールされている更新プログラムのデプロイに関する情報が表示されます。

[ ![Arc 対応サーバーについての Update Management 評価ビュー](./media/view-update-assessments/update-assessment-arc-server.png)](./media/view-update-assessments/update-assessment-arc-server-expanded.png#lightbox)

Automation アカウントからの Arc 対応サーバーを含め、すべてのマシンの更新プログラムの評価を表示するには、 **[Automation アカウント]** に移動し、Update Management が有効になっている Automation アカウントを一覧から選択します。 Automation アカウントで、左側のメニューから **[更新の管理]** を選択します。

ご使用の環境の更新プログラムが、 **[更新の管理]** ページに一覧表示されます。 更新プログラムが不足していると識別された場合は、 **[不足している更新プログラム]** タブに、それらの一覧が表示されます。

[![Update Management の既定のビュー](./media/overview/update-management-view.png)](./media/overview/update-management-view-expanded.png#lightbox)

**[対応]** 列では、マシンが評価された最終時刻を確認できます。 **[エージェントの更新の準備]** 列では、更新エージェントの正常性を確認できます。 問題がある場合は、問題を解決するために役立てることができるトラブルシューティング ドキュメントへのリンクを選択してください。

**[情報リンク]** で、更新プログラムのリンクを選択して、更新プログラムに関する重要な情報を提供するサポート記事を開きます。

[ ![更新ステータスの表示](./media/view-update-assessments/missing-updates.png)](./media/view-update-assessments/missing-updates-expanded.png#lightbox)

更新プログラムのほかの場所をクリックすると、[ログ検索] ペインが開きます。 その更新プログラムについて、ログ検索のクエリが事前に定義されています。 詳細情報を表示するには、このクエリを変更するか、独自のクエリを作成します。

[ ![ログのクエリ結果の表示](./media/view-update-assessments/logsearch-results.png)](./media/view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>不足している更新プログラムを表示する

**[不足している更新プログラム]** をクリックすると、ご利用のマシンで不足している更新プログラムの一覧が表示されます。 各更新プログラムが表示され、選択することができます。 更新プログラムを必要とするマシンの数やオペレーティング システムの詳細、および詳細情報にアクセスするためのリンクが、すべて表示されます。 また、[ログ検索] ペインには、更新プログラムに関する詳細情報も表示されます。

![不足している更新プログラム](./media/view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>更新プログラムの分類の操作

次の表は、Update Management のサポートされる更新プログラムの分類と、各分類の定義を示します。

### <a name="windows"></a>Windows

|分類  |説明  |
|---------|---------|
|緊急更新プログラム     | セキュリティに関連しない重大なバグを修正する、特定の問題に対する更新プログラムです。        |
|セキュリティ更新プログラム     | 製品固有のセキュリティに関連する問題に対する更新プログラムです。        |
|更新プログラムのロールアップ     | 容易なデプロイのためにパッケージにまとめられた修正プログラムのセットです。        |
|Feature Pack     | 製品リリース外で配布される製品の新機能です。        |
|Service Pack     | アプリケーションに適用される修正プログラムのセットです。        |
|定義の更新     | ウイルスまたはその他の定義ファイルに対する更新プログラムです。        |
|ツール     | 1 つまたは複数のタスクを完了するのに役立つユーティリティまたは機能です。        |
|更新プログラム     | 現在インストールされているアプリケーションまたはファイルに対する更新です。        |

### <a name="linux"></a>Linux

|分類  |説明  |
|---------|---------|
|緊急更新プログラムとセキュリティ更新プログラム     | 特定の問題または製品固有のセキュリティに関連する問題に対する更新プログラムです。         |
|他の更新プログラム     | 本質的に重要ではない、またはセキュリティ更新プログラムではない、他のすべての更新プログラムです。        |

Linux の場合、Update Management は、評価データを表示する一方で、クラウド内での重要な更新プログラムとセキュリティ更新プログラムを識別できます (クラウドでのデータ エンリッチメントに起因して、この詳細レベルになる可能性があります)。修正プログラムの場合、Update Management はマシン上にある分類データを使用します。 他のディストリビューションとは異なり、製品の RTM バージョンにおいては、CentOS はこの情報を使用できません。 CentOS マシン上で、次のコマンドに対してセキュリティ データを返すように構成されている場合、Update Management は分類に基づいて修正プログラムを適用できます。

```bash
sudo yum -q --security check-update
```

CentOS 上でネイティブ分類データを使用できるようにするためのサポートされている方法は現在ありません。 現時点では、お客様がこの機能をご自身で有効にした場合には、できる範囲でのサポートのみを提供しています。

Red Hat Enterprise バージョン 6 の更新プログラムを分類するには、yum-security プラグインをインストールする必要があります。 Red Hat Enterprise Linux 7 では、プラグインは既に yum 自体の一部であるため、何もインストールする必要はありません。 詳細については、次の Red Hat の[ナレッジ記事](https://access.redhat.com/solutions/10021)を参照してください。

## <a name="next-steps"></a>次のステップ

このプロセスの次のフェーズでは、[更新プログラムを非対応のマシンにデプロイ](deploy-updates.md)し、展開の結果を確認します。
