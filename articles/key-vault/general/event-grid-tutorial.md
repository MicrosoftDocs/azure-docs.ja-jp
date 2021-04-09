---
title: Azure Event Grid でキー コンテナー通知を受信して応答する
description: Key Vault と Azure Event Grid を統合する方法について説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: d7bb697879f40b45c886cd90bbb1e34906d35f66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96187376"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>Azure Event Grid でキー コンテナー通知を受信して応答する

Azure Key Vault と Azure Event Grid の統合により、キー コンテナーに格納されているシークレットの状態が変更されたときにユーザーに通知することができます。 この機能の概要については、[Event Grid による Key Vault の監視](event-grid-overview.md)に関するページをご覧ください。

このガイドでは、Event Grid によって Key Vault の通知を受信する方法、および Azure Automation によって状態の変更に応答する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- ご自身の Azure サブスクリプションのキー コンテナー。 新しいキー コンテナーをすばやく作成するには、[Azure CLI を使用した Azure Key Vault からのシークレットの設定と取得](../secrets/quick-create-cli.md)に関するページの手順に従います。

## <a name="concepts"></a>概念

Event Grid は、クラウドのイベント処理サービスです。 このガイドの手順に従って、Key Vault のイベントをサブスクライブして、イベントを Automation にルーティングします。 キー コンテナー内でいずれかのシークレットの有効期限がまもなく切れる場合、状態の変更が Event Grid に通知されます。また、エンドポイントへの HTTP POST が実行されます。 その後、Web hook によって PowerShell スクリプトの Automation 実行がトリガーされます。

![HTTP POST フローチャート](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Automation アカウントを作成する

[Azure portal](https://portal.azure.com) で、Automation アカウントを作成します。

1.  portal.azure.com にアクセスして、ご自身のサブスクリプションにログインします。

1.  検索ボックスに、「**Automation Accounts**」と入力します。

1.  検索バーのドロップダウン リストの **[サービス]** セクションで、 **[Automation アカウント]** を選択します。

1.  **[追加]** を選択します。

    ![[Automation アカウント] ウィンドウ](../media/event-grid-tutorial-2.png)

1.  **[Automation アカウントの追加]** ウィンドウに必要な情報を入力し、 **[作成]** を選択します。

## <a name="create-a-runbook"></a>Runbook を作成する

ご自身の Automation アカウントの準備ができたら、Runbook を作成します。

![Runbook UI を作成する](../media/event-grid-tutorial-3.png)

1.  作成したばかりの Automation アカウントを選択します。

1.  **[プロセス オートメーション]** の **[Runbook]** を選択します。

1.  **[Runbook の作成]** を選択します。

1.  Runbook に名前を付けて、Runbook の種類として **[PowerShell]** を選択します。

1.  作成した Runbook を選択し、 **[編集]** ボタンを選択します。

1.  (テストの目的で) 次のコードを入力し、 **[発行]** ボタンを選択します。 このアクションにより、受信した POST 要求の結果が返されます。

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Runbook UI の発行](../media/event-grid-tutorial-4.png)

## <a name="create-a-webhook"></a>webhook を作成する

新しく作成された Runbook をトリガーする Webhook を作成します。

1.  発行したばかりの Runbook の **[リソース]** セクションで **[Webhook]** を選択します。

1.  **[Webhook の追加]** を選択します。

    ![[Webhook の追加] ボタン](../media/event-grid-tutorial-5.png)

1.  **[新しい Webhook を作成します]** を選択します。

1. Webhook に名前を付けて、有効期限を設定し、URL をコピーします。

    > [!IMPORTANT] 
    > Webhook の作成後に URL を表示することはできません。 このガイドの残りの部分で使用できるように、アクセスできる安全な場所にコピーを保管しておいてください。

1. **[パラメーターと実行設定]** を選択し、 **[OK]** を選択します。 パラメーターは入力しないでください。 これにより **[作成]** ボタンが有効になります。

1. **[OK]** を選択し、 **[作成]** を選択します。

    ![新しい Webhook UI の作成](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Event Grid のサブスクリプションを作成する

[Azure portal](https://portal.azure.com) を使用して Event Grid サブスクリプションを作成します。

1.  ご自身のキー コンテナーにアクセスし、**[イベント]** タブを選択します。

    ![Azure portal の [イベント] タブ](../media/event-grid-tutorial-7.png)

1.  **[+ イベント サブスクリプション]** ボタンを選択します。

1.  サブスクリプションのわかりやすい名前を作成します。

1.  **[イベント グリッド スキーマ]** を選択します。

1.  **[トピックのリソース]** は、状態の変化を監視するキー コンテナーにします。

1.  **[イベントの種類のフィルター]** については、すべてのオプションをオンのままにします (**9 個が選択済み**)。

1.  **[エンドポイントの種類]** で **[Webhook]** を選択します。

1.  **[エンドポイントの選択]** を選択します。 新しいコンテキスト ウィンドウの **[サブスクライバー エンドポイント]** フィールドに、「[Webhook を作成する](#create-a-webhook)」手順からの Webhook URL を貼り付けます。

1.  コンテキスト ウィンドウで **[選択内容の確認]** を選択します。

1.  **［作成］** を選択します

    ![イベント サブスクリプションの作成](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>テストして検証する

Event Grid サブスクリプションが適切に構成されていることを確認します。 このテストは、「[Event Grid のサブスクリプションを作成する](#create-an-event-grid-subscription)」で "シークレットの新しいバージョンが作成されました" という通知をサブスクライブしたこと、およびシークレットの新しいバージョンをキー コンテナーに作成するのに必要な権限を持っていることを前提としています。

![Event Grid サブスクリプションの構成のテスト](../media/event-grid-tutorial-9.png)

![[シークレットの作成] ウィンドウ](../media/event-grid-tutorial-10.png)

1.  Azure portal で、ご自身のキー コンテナーに移動します

1.  新しいシークレットを作成します。 テストの目的で、有効期限を翌日に設定します。

1.  ご自身のキー コンテナーの **[イベント]** タブで、作成した Event Grid サブスクリプションを選択します。

1.  **[メトリック]** で、イベントがキャプチャされたかどうかを確認します。 2 つのイベントが想定されます。1 つは SecretNewVersion で、もう 1 つは SecretNearExpiry です。 これらのイベントにより、キー コンテナーにあるシークレットの状態の変更が、Event Grid によって正常にキャプチャされたことが検証されます。

    ![[メトリック] ウィンドウ: キャプチャしたイベントの確認](../media/event-grid-tutorial-11.png)

1.  Automation アカウントに移動します。

1.  **[Runbook]** タブを選択し、作成した Runbook を選択します。

1.  **[Webhook]** タブを選択し、"最終トリガー" のタイム スタンプが、新しいシークレット作成から 60 秒以内であることを確認します。 この結果により、キー コンテナーの状態変更イベントの詳細を含む POST が Event Grid によって Webhook に対して実行され、Webhook がトリガーされたことが確認されます。

    ![[Webhook] タブ、最終トリガー タイムスタンプ](../media/event-grid-tutorial-12.png)

1. ご自身の Runbook に戻り、 **[概要]** タブを選択します。

1. **[最近のジョブ]** の一覧を確認します。 ジョブが作成され、状態が "完了" になっていることがわかります。 これにより、Webhook によって Runbook がトリガーされ、そのスクリプトの実行が開始されたことが確認されます。

    ![Webhook 最近のジョブの一覧](../media/event-grid-tutorial-13.png)

1. 最近のジョブを選択し、Event Grid から Webhook に送信された POST 要求を確認します。 JSON を調べ、キー コンテナーとイベントの種類のパラメーターが正しいことを確認します。 JSON オブジェクトの "イベントの種類" パラメーターが、キー コンテナーで発生したイベント (この例では Microsoft.KeyVault.SecretNearExpiry) と一致する場合、テストは成功しました。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="you-cant-create-an-event-subscription"></a>イベント サブスクリプションを作成できない

お使いの Azure サブスクリプションのリソース プロバイダーで、Event Grid およびキー コンテナー プロバイダーを再登録します。 「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

お疲れさまでした。 これらのすべての手順を正しく実行したら、お使いのキー コンテナーに格納されているシークレットの状態変更にプログラムで応答する準備ができています。

ポーリング ベースのシステムを使用して、キー コンテナー内のシークレットの状態変更を探す場合は、ここでこの通知機能の使用を開始できます。 また、ご自身の Runbook のテスト スクリプトをコードに置き換えて、まもなく有効期限が切れるシークレットをプログラムによって更新することもできます。

詳細情報:


- 概要:[Azure Event Grid での Key Vault の監視](event-grid-overview.md)
- 方法:[キー コンテナーのシークレットが変更されたときにメールを受信する](event-grid-logicapps.md)
- [Azure Key Vault 用の Azure Event Grid イベント スキーマ](../../event-grid/event-schema-key-vault.md)
- [Azure Key Vault の概要](overview.md)
- [Azure Event Grid の概要](../../event-grid/overview.md)
- [Azure Automation の概要](../../automation/index.yml)
