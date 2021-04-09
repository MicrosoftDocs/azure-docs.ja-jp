---
title: Azure Cloud Services (延長サポート) のスケーリングを構成する
description: Azure Cloud Services (延長サポート) のスケーリング オプションを有効にする方法
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cfa5be01a0d36764086c6c9adf97e6cb166d2bb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728163"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Azure Cloud Services (延長サポート) でスケーリング オプションを構成する 

Cloud Services (延長サポート) のデプロイでスケールインとスケールアウトができるように、条件を構成できます。これらの条件は、CPU 使用率、ディスク負荷、およびネットワーク負荷に基づくことができます。 

クラウド サービスのデプロイのスケーリングを構成するときは、次の情報を考慮してください。
- スケーリングは、コア使用量に影響します。 ロール インスタンスが大きいほどコアが多く消費されますが、サブスクリプションのコア制限内でのみスケーリングできます。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。
- キュー メッセージングのしきい値に基づくスケーリングがサポートされています。 詳細については、「[Azure Queue Storage を使用する](../storage/queues/storage-dotnet-how-to-use-queues.md)」をご覧ください。
- クラウド サービス (延長サポート) アプリケーションの高可用性を確保するには、2 つ以上のロール インスタンスを使用してデプロイするようにしてください。
- カスタム自動スケーリングは、すべてのロールが **[準備完了]** 状態にある場合にのみ実行できます。

## <a name="configure-and-manage-scaling"></a>スケーリングを構成して管理する

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. スケーリングを構成するクラウド サービス (延長サポート) デプロイを選択します。 
3. **[スケール]** ブレードを選択します。 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Azure portal での [リモート デスクトップ] オプションの選択を示す画像":::

4. スケーリングを構成できるすべてのロールの一覧がページに表示されます。 構成するロールを選択します。 
5. 構成するスケールの種類を選択します
    - **[手動スケール]** では、インスタンスの絶対数を設定します。
        1. **[手動スケール]** を選択します。
        2. スケールアップまたはスケールダウンするインスタンスの数を入力します。
        3. **[保存]** を選択します。

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Azure portal での手動スケールの設定を示す画像":::

        4. スケーリング操作が直ちに開始されます。 
        
    - **[カスタム自動スケーリング]** を使用すると、スケーリングの程度を制御するルールを設定できます。 
        1. **[カスタム自動スケーリング]** を選択します
        2. メトリックまたはインスタンス数に基づくスケーリングを選択します。

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Azure portal でのカスタム自動スケーリングの設定を示す画像":::

        3. メトリックに基づいてスケーリングする場合は、求められるスケーリング結果を得るために、必要に応じてルールを追加します。

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Azure portal でのカスタム自動スケーリングのルールの設定を示す画像":::

        4. **[保存]** を選択します。
        5. スケーリング操作は、ルールがトリガーされるとすぐに開始されます。
        
6. デプロイに適用された既存のスケーリング ルールを表示または調整するには、 **[スケール]** タブを選択します。

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Azure portal での既存のスケーリング ルールの調整を示す画像":::

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。