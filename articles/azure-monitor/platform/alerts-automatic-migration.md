---
title: Azure Monitor クラシック アラートの自動移行プロセスのしくみについて
description: 自動移行プロセスのしくみについて説明します。
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 6a2d032c6aa33b72fe422638df45ca48bf8b1036
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847285"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>クラシック アラート ルールの自動移行プロセスについて

[以前発表された](monitoring-classic-retirement.md)ように、Azure Monitor の、従来のアラートは廃止されましたが、新しいアラートがまだサポートされていないリソースについては、引き続き制限付きで使用できます。 提供終了プロセスの一環として、Azure portal には、お客様自身で移行を行えるように、[移行ツール](alerts-using-migration-tool.md)が用意されています。
この記事では、自動移行プロセスについて説明します。これは、発生する可能性のある問題の解決に役立ちます。

  > [!NOTE]
  > この記事は、Azure パブリック クラウドにのみ適用されます。 Azure Government クラウドおよび Azure China 21Vianet の Azure Monitor クラシック アラートの提供終了プロセスについては、後日発表される予定です。

## <a name="what-will-happen-during-the-automatic-migration-process"></a>自動移行プロセス中に起こること

- **2019 年 9 月 1 日**以降、[特定のメトリック](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)に対するものを例外として、新しいクラシック アラート ルールを作成することはできなくなります。
- この例外に該当する場合、お客様は追って発表されるまでクラシック アラート ルールの新規作成とクラシック アラートの使用を継続できます。
- **2019 年 9 月 1 日**以降、クラシック アラートの移行は、クラシック アラートを使用するすべてのお客様に対して一括で実行されます。
- 移行できない特定のクラシック アラート ルールは、自主的移行ツールを使用する場合と同様にそのまま残されます。 これらのクラシック アラート ルールは、追って発表されるまでは引き続きサポートされます。 ただし、無効なクラシック アラート ルールは機能しないため、削除されます。
削除されたターゲット リソースを監視しているクラシック アラート ルールや、[サポートされなくなったメトリック](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics)に対するクラシック アラート ルールは、無効と見なされます。
- ご使用のサブスクリプションの移行が開始されると、問題が発生しない限り、移行は 1 時間以内に完了します。 お客様は [Azure Monitor の移行ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)で移行の状態を監視できます。
- サブスクリプションの所有者は、移行が正常に完了した時にメールを受信します。
- 移行中に問題が発生した場合、サブスクリプションの所有者はその旨を通知するメールも受信します。 お客様は、移行ブレードを使用して、問題の詳細を確認できます。
- リソース ロックの一時的な無効化や、ポリシー割り当ての変更など、お客様の対処が必要な場合は、それらの問題を解決する必要があります。 それまでに問題が解決されない場合は、クラシック アラートの正常な移行を保証できません。

    > [!NOTE]
    > 自動移行プロセスの開始を待てない場合は、移行ツールを使用して自主的に移行を行うこともできます。

## <a name="important-things-to-note"></a>重要な注意事項

移行プロセスで、クラシック アラート ルールを新たな同等のアラート ルールに変換して、アクション グループを作成します。 準備中には、次の点に注意してください。

- クラシック アラート ルールでは、より多くの機能がサポートされているため、新しいアラート ルールの通知ペイロード形式は、クラシック アラート ルールの形式とは異なります。 クラシック アラート ルールによってトリガーされるロジック アプリ、Runbook、または Webhook がある場合、通知ペイロードの違いのために、移行の完了後にそれらが予期したとおりに機能しなくなる可能性があります。 [移行のための準備を行う方法について](alerts-prepare-migration.md)。

- クラシック アラート ルールの中には、ツールを使用して移行できないものもあります。 [移行できないルールと、それらの対処方法についてご確認ください](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)。

    > [!NOTE]
    > 移行プロセスは、ご利用のクラシック アラート ルールの評価に影響ありません。 移行されて新しいアラート ルールが有効になるまで、それらは引き続き実行され、アラートを送信します。

## <a name="what-if-the-automatic-migration-fails"></a>自動移行に失敗した場合

自動移行プロセスが失敗すると、サブスクリプションの所有者は、問題を通知するメールを受信します。 Azure Monitor の移行ブレードを使用して、問題の詳細を確認できます。

移行中に直面する可能性がある問題のヘルプについては、[トラブルシューティング ガイド](alerts-understand-migration.md#common-problems-and-remedies)を参照してください。

  > [!NOTE]
  > リソース ロックの一時的な無効化や、ポリシー割り当ての変更など、お客様の対処が必要な場合は、それらの問題を解決する必要があります。 それまでに問題が解決されない場合は、クラシック アラートの正常な移行を保証できません。

## <a name="next-steps"></a>次のステップ

- [移行を準備する](alerts-prepare-migration.md)
- [移行ツールの動作の理解](alerts-understand-migration.md)
