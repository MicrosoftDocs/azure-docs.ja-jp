---
title: シークレットの Key Vault 状態が変更されたときにメールを送信する
description: Logic Apps を使用して Key Vault シークレットの変更に応答するためのガイド
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 9c522d870a25b3df34ab6a0cf1c1e944a6462685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013991"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Logic Apps を使用して Key Vault シークレットの状態変更に関するメールを受け取る

このガイドでは、[Azure Logic Apps](../../logic-apps/index.yml) を使用して、[Azure Event Grid](../../event-grid/index.yml) 経由で受信した Azure Key Vault イベントに応答する方法について説明します。 最終的に、Azure Key Vault でシークレットが作成されるたびに通知メールを送信するように Azure Logic Apps が設定されます。

Azure Key Vault と Azure Event Grid の統合の概要については、「[Azure Event Grid による Key Vault の監視](event-grid-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Logic Apps がサポートするメール プロバイダー (Office 365 Outlook など) のメール アカウント。 このメール アカウントは、イベント通知の送信に使われます。 サポートされている Logic App コネクタの完全な一覧については、「[コネクタの概要](/connectors)」をご覧ください
- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- ご自身の Azure サブスクリプションのキー コンテナー。 新しいキー コンテナーをすばやく作成するには、[Azure CLI を使用した Azure Key Vault からのシークレットの設定と取得](../secrets/quick-create-cli.md)に関するページの手順に従います。
- リソース プロバイダーとして登録済みの Event Grid。[リソース プロバイダーの登録](../../azure-resource-manager/management/resource-providers-and-types.md)に関するページを参照してください

## <a name="create-a-logic-app-via-event-grid"></a>Event Grid を使用してロジック アプリを作成する

最初に、Event Grid ハンドラーでロジック アプリを作成し、Azure Key Vault の "SecretNewVersionCreated" イベントをサブスクライブします。

Azure Event Grid サブスクリプションを作成するには、次の手順に従います。

1. Azure portal で、キー コンテナーに移動し、**[イベント] > [はじめに]** を選択して **[Logic Apps]** をクリックします

    
    ![Key Vault - [イベント] ページ](../media/eventgrid-logicapps-kvsubs.png)

1. **Logic Apps デザイナー** で接続を検証し、**[続行]** をクリックします。 
 
    ![Logic Apps デザイナー - 接続](../media/eventgrid-logicappdesigner1.png)

1. **[リソース イベントが発生したとき]** 画面で、次の手順を実行します。
    - **[サブスクリプション]** と **[リソース名]** は既定値のままにします。
    - **[リソースの種類]** で **[Microsoft.KeyVault.vaults]** を選択します。
    - **[Event Type Item - 1]\(イベントの種類の項目 - 1\)** で **[Microsoft.KeyVault.SecretNewVersionCreated]** を選択します。

    ![Logic Apps デザイナー - イベント ハンドラー](../media/eventgrid-logicappdesigner2.png)

1. **[+ 新しいステップ]** を選択します。[アクションを選択してください] ウィンドウが開きます。
1. **[電子メール]** を検索します。 電子メール プロバイダーに基づいて、一致するコネクタを検索して選択します。 このチュートリアルでは、**Office 365 Outlook** を使います。 他のメール プロバイダーの手順も同様です。
1. **[メールの送信 (V2)]** アクションを選択します。

   ![Logic Apps デザイナー - メールの送信](../media/eventgrid-logicappdesigner3.png)

1. メール テンプレートを作成します。
    - **To:** 通知メールを受信するメール アドレスを入力します。 このチュートリアルでは、テストの目的でアクセスできる電子メール アカウントを使用します。
    - **[件名]** と **[本文]**: メールのテキストを記述します。 イベント データに基づく動的なコンテンツを挿入するには、セレクター ツールから JSON プロパティを選択します。 イベントのデータを取得するには、`@{triggerBody()?['Data']}` を使用します。

    メール テンプレートは次の例のようになります。

    ![Logic Apps デザイナー - メールの本文](../media/eventgrid-logicappdesigner4.png)

8. **[名前を付けて保存]** をクリックします。
9. 新しいロジック アプリの **[名前]** を入力し、**[作成]** をクリックします。
    
    ![Logic Apps デザイナー - 作成](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>テストして検証する

1.  Azure portal でキー コンテナーにアクセスし、**[イベント] > [イベント サブスクリプション]** を選択します。  新しいサブスクリプションが作成されたことを確認します。
    
    ![Logic Apps デザイナー - テストと検証](../media/eventgrid-logicapps-kvnewsubs.png)

1.  キー コンテナーにアクセスして **[シークレット]** を選択し、**[Generate/Import]\(生成/インポート\)** を選択します。 テスト用に新しいシークレットを作成し、キーに名前を付け、残りのパラメーターは既定の設定のままにします。

    ![キー コンテナー - シークレットの作成](../media/eventgrid-logicapps-kv-create-secret.png)

1. **[シークレットの作成]** 画面で、任意の名前と任意の値を指定し、**[作成]** を選択します。

シークレットが作成されると、構成したアドレスでメールを受信します。

## <a name="next-steps"></a>次のステップ

- 概要:[Azure Event Grid での Key Vault の監視](event-grid-overview.md)
- 方法:[Azure Automation へのキー コンテナー通知のルーティング](event-grid-tutorial.md)
- [Azure Key Vault 用の Azure Event Grid イベント スキーマ](../../event-grid/event-schema-key-vault.md)
- [Azure Event Grid](../../event-grid/index.yml) について学習します。
- [Azure App Service の Logic Apps 機能](../../logic-apps/index.yml)について学習します。