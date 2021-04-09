---
title: Just-In-Time アクセスを要求する
description: Azure Managed Applications の発行元が、マネージド アプリケーションへのジャストインタイム アクセスを要求する方法について説明します。
author: MSEvanhi
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: evanhi
ms.openlocfilehash: b7c067ca82ea7fbe7eb9c0f3d50b7fe75dfb47df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377365"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Azure Managed Applications でジャストインタイム アクセスを有効にして要求する

マネージド アプリケーションのコンシューマーが、マネージド リソース グループへの永続的なアクセスを発行元に許可することに消極的な場合があります。 マネージド アプリケーションの発行元としては、管理対象リソースに厳密にいつアクセスする必要があるかをコンシューマーに知らせることが望ましい場合があります。 コンシューマーが管理対象リソースへのアクセス権の付与を制御できるようにするため、Azure Managed Applications には、ジャストインタイム (JIT) アクセスと呼ばれる機能が提供されています。 現在、この機能はプレビュー段階にあります。

JIT アクセスによって、発行元は、トラブルシューティングやメンテナンスを行うために、マネージド アプリケーションのリソースに対して昇格されたアクセス権を要求することができます。 発行元は、リソースへの読み取り専用アクセス権を常に所有していますが、特定の期間にさらに大きなアクセス権を持つことができます。

アクセス権を付与するためのワークフローは次のとおりです。

1. 発行元が、マーケットプレースにマネージド アプリケーションを追加し、JIT アクセスを使用できることを指定します。

1. デプロイ時に、コンシューマーが、マネージド アプリケーションのインスタンスに対する JIT アクセスを有効にします。

1. デプロイ後に、コンシューマーは、JIT アクセスの設定を変更できます。

1. 発行元が、マネージド リソースのトラブルシューティングや更新が必要なときに、アクセス要求を送信します。

1. コンシューマーが、要求を承認します。

この記事では、JIT アクセスを有効にし、要求を送信するために発行元が行う操作について説明します。 JIT アクセス要求の承認については、[Azure Managed Applications でのジャストインタイム アクセスの承認](approve-just-in-time-access.md)に関する記事を参照してください。

## <a name="add-jit-access-step-to-ui"></a>JIT アクセス手順を UI に追加する

CreateUiDefinition.json ファイルに、コンシューマーが JIT アクセスを有効にできる手順を含めます。 オファリングで JIT 機能をサポートするには、CreateUiDefinition.json ファイルに次の内容を追加します。

"steps" に:

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```

"outputs" に:

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT アクセスはプレビュー段階です。 JIT 構成のスキーマは、将来の反復過程で変更される可能性があります。

## <a name="enable-jit-access"></a>JIT アクセスを有効化する

パートナー センターでオファーを作成するとき、JIT アクセスを必ず有効にしてください。

1. [パートナー センター](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)のコマーシャル マーケットプレース ポータルにサインインします。

1. 新しいマネージド アプリケーションを作成する方法については、「[Azure アプリケーション オファーを作成する](../../marketplace/create-new-azure-apps-offer.md)」の手順に従ってください。

1. **技術的な構成** ページで、 **[ジャストインタイム (JIT) アクセスを有効にする]** チェックボックスを選択します。

   :::image type="content" source="./media/request-just-in-time-access/enable-just-in-time-access.png" alt-text="ジャストインタイム アクセスを有効にする":::

JIT 構成手順が UI に追加され、コマーシャル マーケットプレースのオファリングで JIT アクセスが有効になっています。 コンシューマーは、マネージド アプリケーションをデプロイするときに、[インスタンスに対する JIT アクセスをオンにする](approve-just-in-time-access.md#enable-during-deployment)ことができます。

## <a name="request-access"></a>アクセスの要求

発行元は、コンシューマーの管理対象リソースにアクセスする必要があるときに、特定のロール、時刻、期間の要求を送信します。 コンシューマーは、その要求を承認する必要があります。

JIT アクセス要求を送信するには:

1. アクセスする必要があるマネージド アプリケーションで **[JIT アクセス]** を選択します。

1. **[資格のあるロール]** を選択し、目的のロールの [アクション] 列で **[アクティブ化]** を選択します。

   ![アクセス要求をアクティブ化する](./media/request-just-in-time-access/send-request.png)

1. **[ロールのアクティブ化]** フォームで、ロールをアクティブにする開始時刻と期間を選択します。 **[アクティブ化]**  を選択して要求を送信します。

   ![アクセスをアクティブ化する](./media/request-just-in-time-access/activate-access.png) 

1. 新しい JIT 要求がコンシューマーに正常に送信されたことを示す通知を表示して確認します。

   ![Notification](./media/request-just-in-time-access/in-progress.png)

   これで、コンシューマーが[要求が承認する](approve-just-in-time-access.md#approve-requests)まで待機する必要があります。

1. マネージド アプリケーションのすべての JIT 要求の状態を表示するには、 **[JIT アクセス]** と **[要求履歴]** を選択します。

   ![状態の表示](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>既知の問題

マネージド アプリケーション定義の中に、JIT アクセスを要求するアカウントのプリンシパル ID を明示的に含める必要があります。 パッケージに指定されるグループを介してのみアカウントを含めることはできません。 この制限は、今後のリリースで解消される予定です。

## <a name="next-steps"></a>次のステップ

JIT アクセス要求の承認については、[Azure Managed Applications でのジャストインタイム アクセスの承認](approve-just-in-time-access.md)に関する記事を参照してください。
