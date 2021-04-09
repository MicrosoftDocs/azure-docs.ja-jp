---
title: Azure Site Recovery プロセス サーバーを監視する
description: この記事では、VMware VM/物理サーバーのディザスター リカバリーに使用される Azure Site Recovery プロセス サーバーを監視する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: eebaa70cee99380ac67b8f6516a5b08ff2832c86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134741"
---
# <a name="monitor-the-process-server"></a>プロセス サーバーを監視する

この記事では、[Site Recovery](site-recovery-overview.md) プロセス サーバーを監視する方法について説明します。

- プロセス サーバーは、オンプレミスの VMware VM と物理サーバーのディザスター リカバリーを Azure に対して設定するときに使用されます。
- 既定では、プロセス サーバーは構成サーバーで実行されます。 構成サーバーをデプロイするときに、既定でインストールされます。
- 必要に応じて、多数のレプリケートされたマシンと大量のレプリケーション トラフィックをスケーリングして処理するために、追加のスケールアウト プロセス サーバーをデプロイできます。

プロセス サーバーのロールとデプロイの詳細については、[こちら](vmware-physical-azure-config-process-server-overview.md)をご覧ください。

## <a name="monitoring-overview"></a>監視の概要

プロセス サーバーには、レプリケートされたデータ キャッシュ、圧縮、および Azure への転送では特に多くのロールがあるため、継続的にプロセス サーバーの正常性を監視する必要があります。

プロセス サーバーのパフォーマンスに影響を及ぼしやすい状況は数多くあります。 パフォーマンスに影響する問題は、VM の正常性に連鎖的な影響を及ぼし、最終的にプロセス サーバーとそのレプリケートされたマシンの両方が深刻な状態になります。 次のような状況があります。

- 多数の VM がプロセス サーバーを使用していて、推奨される制限に近づいているか、制限を超えている。
- プロセス サーバーを使用している VM に高いチャーン レートがある。
- VM とプロセス サーバー間のネットワーク スループットが、プロセス サーバーにレプリケーション データをアップロードするには不十分である。
- プロセス サーバーと Azure 間のネットワーク スループットが、プロセス サーバーから Azure にレプリケーション データをアップロードするには不十分である。

これらすべての問題が VM の回復ポイントの目標 (RPO) に影響する可能性があります。 

**その理由は**、 VM の回復ポイントを生成するには、VM のすべてのディスクに共通ポイントが必要だからです。 1 つのディスクに高いチャーン レートがある、レプリケーションが遅い、またはプロセス サーバーが最適でない場合、回復ポイントの効率的な作成方法に影響します。

## <a name="monitor-proactively"></a>予防的な監視

プロセス サーバーの問題を回避するには、以下を行うことが重要です。

- [容量とサイジングのガイダンス](site-recovery-plan-capacity-vmware.md#capacity-considerations)を使用して、プロセス サーバーの特定の要件を理解し、プロセス サーバーがデプロイされ、推奨事項に従って実行されていることを確認する。
- アラートを監視し、問題が発生したときにトラブルシューティングを行い、効率的に実行されるようにプロセス サーバーを維持する。


## <a name="process-server-alerts"></a>プロセス サーバー アラート

プロセス サーバーで生成される多くの正常性アラートを次の表にまとめました。

**アラートの種類** | **詳細**
--- | ---
![Healthy][green] | プロセス サーバーは接続されており、正常な状態です。
![警告][yellow] | 過去 15 分間の CPU 使用率が 80% を超えています。
![警告][yellow] | 過去 15 分間のメモリ使用率が 80% を超えています。
![警告][yellow] | 過去 15 分間のキャッシュ フォルダーの空き領域が 30% 未満です。
![警告][yellow] | 保留または送信データを 5 分ごとに監視している Site Recovery によって、プロセス サーバー キャッシュ内のデータを 30 分以内に Azure にアップロードできないと推定された。
![警告][yellow] | プロセス サーバーのサービスが過去 15 分間実行されていません。
![Critical][red] | 過去 15 分間の CPU 使用率が 95% を超えています。
![Critical][red] | 過去 15 分間のメモリ使用率が 95% を超えています。
![Critical][red] | 過去 15 分間のキャッシュ フォルダーの空き領域が 25% 未満です。
![Critical][red] | 保留または送信データを 5 分ごとに監視している Site Recovery によって、プロセス サーバー キャッシュ内のデータを 45 分以内に Azure にアップロードできないと推定された。
![Critical][red] | プロセス サーバーからのハートビートが 15 分間ありません。

![テーブル キー](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> プロセス サーバーの全体的な正常性状態は、生成された最悪のアラートに基づいています。



## <a name="monitor-process-server-health"></a>プロセス サーバーの正常性を監視する

プロセス サーバーの正常性状態は、次のように監視できます。 

1. レプリケーションの正常性、およびレプリケートされたマシンとそのプロセス サーバーの状態を監視するには、コンテナーで **[レプリケートされたアイテム]** を選択し、監視するマシンをクリックします。
2. **[レプリケーションの正常性]** では、VM の正常性状態を監視することができます。 エラーの詳細をドリルダウンするには、状態をクリックします。

    ![VM のダッシュボードでのプロセス サーバーの正常性](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. **[プロセス サーバーの正常性]** では、プロセス サーバーの状態を監視できます。 詳細をドリルダウンします。

    ![VM のダッシュボードでのプロセス サーバーの詳細](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. 正常性は、[VM] ページのグラフィカル表示を使用して監視することもできます。
    - スケール アウト プロセス サーバーは、それに関連付けられている警告がある場合はオレンジ色で、重大な問題がある場合は赤色で強調表示されます。 
    - プロセス サーバーが構成サーバーで既定のデプロイで実行されている場合は、それに応じて構成サーバーが強調表示されます。
    - ドリルダウンするには、構成サーバーまたはプロセス サーバーをクリックします。 問題点および修復の推奨事項があれば、メモします。

**[Site Recovery インフラストラクチャ]** の下のコンテナーで、プロセス サーバーを監視することもできます。 **[Site Recovery インフラストラクチャの管理]** で、 **[構成サーバー]** をクリックします。 プロセス サーバーに関連付けられている構成サーバーを選択して、プロセス サーバーの詳細をドリル ダウンします。


## <a name="next-steps"></a>次のステップ

- プロセス サーバーの問題がある場合は、[トラブルシューティング ガイダンス](vmware-physical-azure-troubleshoot-process-server.md)に従います。
- さらにサポートが必要な場合は、[Azure Site Recovery に関する Microsoft Q&A 質問ページ](/answers/topics/azure-site-recovery.html)に質問を投稿してください。 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
