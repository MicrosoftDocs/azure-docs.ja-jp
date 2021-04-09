---
title: Azure Monitor クラシック アラートの自動移行プロセスのしくみについて
description: 自動移行プロセスのしくみについて説明します。
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 92aaffcea7a7c96cd77aade318520b093eed3e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045464"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>クラシック アラート ルールの自動移行プロセスについて

[以前発表した](monitoring-classic-retirement.md)ように、Azure Monitor のクラシック アラートがパブリック クラウド ユーザーで廃止されましたが、**2021 年 5 月 31 日** までは、引き続き制限付きで使用できます。 Azure Government クラウドおよび Azure China 21Vianet 向けの従来のアラートは、**2024 年 2 月 29 日** に廃止されます。

Azure portal には、お客様自身で移行を行えるように、[移行ツール](alerts-using-migration-tool.md)が用意されています。 この記事では、2021 年 5 月 31 日以降に開始される、パブリック クラウドでの自動移行プロセスについて説明します。 また、発生する可能性がある問題と解決策についても詳しく説明します。

## <a name="important-things-to-note"></a>重要な注意事項

移行プロセスで、クラシック アラート ルールを新たな同等のアラート ルールに変換して、アクション グループを作成します。 準備中には、次の点に注意してください。

- クラシック アラート ルールでは、より多くの機能がサポートされているため、新しいアラート ルールの通知ペイロード形式は、クラシック アラート ルールのペイロードとは異なります。 ロジック アプリ、Runbook、または Webhook に関するクラシック アラート ルールがある場合、ペイロードの違いのため、移行後に予期したとおりに機能しなくなる可能性があります。 [移行のための準備を行う方法について](alerts-prepare-migration.md)。

- クラシック アラート ルールの中には、ツールを使用して移行できないものもあります。 [移行できないルールと、それらの対処方法についてご確認ください](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)。

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>パブリック クラウドでの自動移行プロセス中に行われること

- 2021 年5 月 31 日以降、新しいクラシック アラート ルールを作成できなくなり、クラシック アラートの移行はバッチでトリガーされます。
- 削除されたターゲット リソースを監視しているクラシック アラート ルールや、[サポートされなくなったメトリック](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics)に対するクラシック アラート ルールは、無効と見なされます。
- 無効なクラシック アラート ルールは、2021 年 5 月 31 日以降に削除されます。
- サブスクリプションの移行は、開始されると 1 時間以内に完了するはずです。 お客様は [Azure Monitor の移行ツール](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)で移行の状態を監視できます。
- サブスクリプションの所有者は、移行が成功または失敗するとメールを受け取ります。

    > [!NOTE]
    > 自動移行プロセスの開始を待てない場合は、移行ツールを使用して自主的に移行を行うこともできます。

## <a name="what-if-the-automatic-migration-fails"></a>自動移行に失敗した場合

自動移行プロセスが失敗すると、サブスクリプションの所有者は、問題を通知するメールを受信します。 Azure Monitor の移行ツールを使用して、問題の詳細を確認できます。 移行中に直面する可能性がある問題のヘルプについては、[トラブルシューティング ガイド](alerts-understand-migration.md#common-problems-and-remedies)を参照してください。

  > [!NOTE]
  > リソース ロックの一時的な無効化や、ポリシー割り当ての変更など、お客様の対処が必要な場合は、それらの問題を解決する必要があります。 それまでに問題が解決されない場合は、クラシック アラートの正常な移行を保証できません。

## <a name="next-steps"></a>次のステップ

- [移行を準備する](alerts-prepare-migration.md)
- [移行ツールの動作の理解](alerts-understand-migration.md)