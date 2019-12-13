---
title: Azure Update Management で更新プログラムの評価を表示する
description: この記事では、更新プログラムの展開に関して、更新プログラムの評価を表示する方法について説明します。
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d27df57e9371f16a15d3a18b7722598062377d88
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850518"
---
# <a name="view-azure-update-management-update-assessments"></a>Azure Update Management で更新プログラムの評価を表示する

Azure Automation アカウントで **[Update Management]** をクリックすると、使用しているマシンの状態が表示されます。

このビューには、ご利用のマシン、不足している更新プログラム、更新プログラムのデプロイ、およびスケジュールされている更新プログラムのデプロイに関する情報が表示されます。 **[対応]** 列では、マシンが評価された最終時刻を確認できます。 **[エージェントの更新の準備]** 列では、更新エージェントの正常性を確認できます。 問題がある場合は、問題を解決するために役立てることができるトラブルシューティング ドキュメントへのリンクを選択してください。

マシン、更新プログラム、またはデプロイに関する情報を返すログ検索を実行するには、一覧から該当する項目を選択します。 これにより、項目のクエリが選択された状態で **[ログ検索]** ページが開きます。

![Update Management の既定のビュー](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>不足している更新プログラムを表示する

**[不足している更新プログラム]** をクリックすると、ご利用のマシンで不足している更新プログラムの一覧が表示されます。 各更新プログラムが表示され、選択することができます。 更新プログラムを必要とするマシンの数やオペレーティング システムの詳細、および詳細情報にアクセスするためのリンクが、すべて表示されます。 また、 **[ログ検索]** ペインには、更新プログラムに関する詳細情報も表示されます。

![不足している更新プログラム](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>更新プログラムの分類

次の表は、Update Management の更新プログラムの分類と、各分類の定義を示します。

### <a name="windows"></a>Windows

|分類  |説明  |
|---------|---------|
|緊急更新プログラム     | セキュリティに関連しない重大なバグを修正する、特定の問題に対する更新プログラムです。        |
|セキュリティ更新プログラム     | 製品固有のセキュリティに関連する問題に対する更新プログラムです。        |
|更新プログラムのロールアップ     | 容易なデプロイのためにパッケージにまとめられた修正プログラムの累積セットです。        |
|Feature Pack     | 製品リリース外で配布される製品の新機能です。        |
|Service Pack     | アプリケーションに適用される修正プログラムの累積セットです。        |
|定義の更新     | ウイルスまたは他の定義ファイルに対する更新プログラムです。        |
|ツール     | 1 つまたは複数のタスクを完了するのに役立つユーティリティまたは機能です。        |
|更新プログラム     | 現在インストールされているアプリケーションまたはファイルに対する更新プログラムです。        |

### <a name="linux-2"></a>Linux

|分類  |説明  |
|---------|---------|
|緊急更新プログラムとセキュリティ更新プログラム     | 特定の問題または製品固有のセキュリティに関連する問題に対する更新プログラムです。         |
|他の更新プログラム     | 本質的に重要ではない、またはセキュリティ更新プログラムではない、他のすべての更新プログラムです。        |

Linux の場合、Update Management は、評価データを表示する一方で、クラウド内での重要な更新プログラムとセキュリティ更新プログラムを識別できます (クラウドでのデータ エンリッチメントに起因して、この詳細レベルになる可能性があります)。修正プログラムの場合、Update Management はマシン上にある分類データを使用します。 他のディストリビューションとは異なり、製品の RTM バージョンにおいては、CentOS はこの情報を使用できません。 CentOS マシン上で、次のコマンドに対してセキュリティ データを返すように構成されている場合、Update Management は分類に基づいて修正プログラムを適用できます。

```bash
sudo yum -q --security check-update
```

CentOS 上でネイティブ分類データを使用できるようにするためのサポートされている方法は現在ありません。 現時点では、お客様がこの機能をご自身で有効にした場合には、できる範囲でのサポートのみを提供しています。

## <a name="next-steps"></a>次の手順

更新プログラムの評価を閲覧した後、[Azure VM 用の更新プログラムと修正プログラムの管理](automation-tutorial-update-management.md)に関するページの手順に従って、更新プログラムの展開をスケジュールできます。
