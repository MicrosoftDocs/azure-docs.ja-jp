---
title: Azure Automation で VM の更新プログラムとパッチを管理する
description: この記事では、Update Management を使用して Azure および Azure 以外の VM の更新プログラムとパッチを管理する方法を説明します。
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: e4f630931e3cd79a46a539955b45d72e5b573271
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449663"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>VM の更新プログラムとパッチを管理する

Azure Automation Update Management のソフトウェア更新プログラムには、ソフトウェア更新プログラムを追跡し、Azure およびハイブリッド クラウドのマシンに適用する複雑なタスクの管理に役立つツールとリソースのセットが用意されています。 運用効率を維持し、セキュリティ上の問題を解決し、増加するサイバー セキュリティの脅威によるリスクを軽減するためには、効果的なソフトウェア更新プログラムの管理プロセスが必要です。 ただし、テクノロジは常に変化し、新しいセキュリティ上の脅威が絶え間なく出現するために、効果的なソフトウェア更新プログラムの管理には、一貫した継続的な注意が必要です。

> [!NOTE]
> Update Management では、ファーストパーティの更新プログラムの展開と、それらの事前ダウンロードがサポートされています。 このサポートには、更新プログラムの適用対象のシステムに対する変更が必要になります。 お使いのシステムでこれらの設定を構成する方法については、「[Azure Automation Update Management 用に Windows Update の設定を構成する](update-mgmt-configure-wuagent.md)」を参照してください。

VM の更新プログラムを管理する前に、次のいずれかの方法を使用して、それらに対して Update Management を有効にしていることを確認してください。

* [Automation アカウントから Update Management を有効にする](update-mgmt-enable-automation-account.md)
* [Azure portal を参照して Update Management を有効にする](update-mgmt-enable-portal.md)
* [Runbook から Update Management を有効にする](update-mgmt-enable-runbook.md)
* [Azure VM から Update Management を有効にする](update-mgmt-enable-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>展開のスコープを制限する

Update Management では、ワークスペース内のスコープ構成を使用して、更新プログラムを受信するコンピューターを対象として指定します。 詳細については、[Update Management の展開スコープの制限](update-mgmt-scope-configuration.md)に関するページを参照してください。

## <a name="compliance-assessment"></a>コンプライアンス評価

ソフトウェア更新プログラムをマシンに展開する前に、有効化されたマシンに対する更新プログラムのコンプライアンス評価の結果を確認します。 各ソフトウェア更新プログラムのコンプライアンスの状態は記録され、評価が完了した後に収集されて一括で Azure Monitor ログに転送されます。

Windows マシンでは、コンプライアンス スキャンは既定で 12 時間ごとに実行されます。 このスケジュール化されたスキャンに加えて、Windows 用の Log Analytics エージェントの再起動後 15 分以内、更新プログラムのインストール前、および更新プログラムのインストール後に、更新プログラムのコンプライアンスを確認するためのスキャンが開始されます。 また、Update Management を使用して [Windows Update クライアントを構成する](update-mgmt-configure-wuagent.md)方法に関する推奨事項を確認し、これが適切に管理されるのを妨げる問題を回避することも重要です。

Linux マシンでは、コンプライアンス スキャンは既定で 1 時間ごとに実行されます。 Linux 用 Log Analytics エージェントを再起動した場合、コンプライアンス スキャンは 15 分以内に開始されます。

コンプライアンスの結果は、評価した各マシンごとに Update Management に表示されます。 管理のために有効化された新しいマシンでは、ダッシュボードに更新されたデータが表示されるまでに最大で 30 分かかることがあります。

コンプライアンスの結果を表示する方法については、[ソフトウェア更新プログラムの監視](update-mgmt-view-update-assessments.md)に関する記事を参照してください。

## <a name="deploy-updates"></a>更新プログラムの展開

コンプライアンスの結果を確認した後のソフトウェア更新プログラムの展開フェーズは、ソフトウェア更新プログラムを展開するプロセスです。 更新プログラムをインストールするには、リリース スケジュールとサービス期間と合致する展開をスケジュールします。 デプロイに含める更新の種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

更新プログラムの展開をスケジュールする方法については、[ソフトウェア更新プログラムの展開](update-mgmt-deploy-updates.md)に関する記事を参照してください。

## <a name="review-update-deployments"></a>更新プログラムの展開を確認する

展開が完了したら、プロセスを確認して、マシンまたはターゲット グループごとに更新プログラムの展開が成功したかどうかを確認します。 展開ステータスを監視する方法については、[展開ステータスの確認](update-mgmt-deploy-updates.md#check-deployment-status)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* 更新プログラムの展開の結果について通知するアラートを作成する方法については、[Update Management のアラートを作成](update-mgmt-configure-alerts.md)に関する記事を参照してください。

* [Azure Monitor ログに対してクエリを実行](update-mgmt-query-logs.md)すると、更新プログラムの評価、展開、およびその他の関連する管理タスクを分析することができます。 これには、作業を開始するために役立つ事前定義されたクエリが含まれています。