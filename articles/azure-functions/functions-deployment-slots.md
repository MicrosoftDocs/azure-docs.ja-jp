---
title: Azure Functions デプロイ スロット
description: Azure Functions でデプロイ スロットを作成して使用する方法を学習します
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 2dbf7c31e6b0b40fa9dc2d59e86c0ecc731657e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172411"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions デプロイ スロット

Azure Functions デプロイ スロットを使用すると、関数アプリで "スロット" と呼ばれる別のインスタンスを実行できます。 スロットは、一般公開されているエンドポイントを介して公開されるさまざまな環境です。 1 つのアプリ インスタンスが常に運用スロットにマップされ、必要に応じて、スロットに割り当てられたインスタンスをスワップできます。 App Service プランで実行されている関数アプリには複数のスロットが含まれる場合がありますが、従量課金プランでは 1 つのスロットのみが許可されます。

スロットのスワップによって関数がどのように影響を受けるかを以下に示します。

- トラフィックのリダイレクトはシームレスです。スワップが原因で要求が削除されることはありません。
- スワップ中に関数が実行されている場合は、実行が続行され、それ以降のトリガーがスワップされたアプリ インスタンスにルーティングされます。

> [!NOTE]
> スロットは現時点で、Linux 従量課金プランでは使用できません。

## <a name="why-use-slots"></a>スロットを使用する理由

デプロイ スロットの使用には多くの利点があります。 次のシナリオでは、スロットの一般的な使用法について説明します。

- **さまざまな目的に応じたさまざまな環境**:さまざまなスロットを使用することで、運用またはステージング スロットにスワップする前に、アプリ インスタンスを区別できます。
- **事前ウォーミング**:直接運用環境にではなく、スロットにデプロイすると、アプリをウォームアップしてから運用を開始することができます。 さらに、スロットを使用すると、HTTP によってトリガーされるワークロードの待機時間が短縮されます。 インスタンスはデプロイ前にウォームアップされるため、新しくデプロイされた関数のコールド スタートが減少します。
- **簡単なフォールバック**:運用環境とのスワップ後も、以前にアプリがステージングされたスロットに、以前の運用アプリが存在します。 運用スロットにスワップされた変更が期待どおりでない場合は、すぐにスワップを逆にして、"最新の既知の良好なインスタンス" に戻すことができます。

## <a name="swap-operations"></a>スワップ操作

スワップ中は、一方のスロットがソースと見なされ、もう一方がターゲットと見なされます。 ソース スロットには、ターゲット スロットに適用されるアプリケーションのインスタンスがあります。 次の手順では、スワップ中にターゲット スロットでダウンタイムが発生しないようにします。

1. **設定を適用する:** ターゲット スロットからの設定は、ソース スロットのすべてのインスタンスに適用されます。 たとえば、運用設定はステージング インスタンスに適用されます。 適用される設定には次のカテゴリが含まれます。
    - [スロット固有](#manage-settings)のアプリ設定と接続文字列 (該当する場合)
    - [継続的デプロイ](../app-service/deploy-continuous-deployment.md)の設定 (有効な場合)
    - [App Service 認証](../app-service/overview-authentication-authorization.md)の設定 (有効な場合)

1. **再起動され、使用できるようになるまで待機する:** スワップでは、ソース スロット内のすべてのインスタンスの再起動が完了し、要求で使用できるようになるまで待機します。 いずれかのインスタンスが再起動に失敗した場合、スワップ操作ではすべての変更がソース スロットに戻されて、操作が停止されます。

1. **ルーティングを更新する:** ソース スロットのすべてのインスタンスが正常にウォームアップされている場合、2 つのスロットでは、ルーティング規則を切り替えてスワップを完了します。 この手順の後は、前にソース スロットでウォーム アップされたアプリはターゲット スロット (運用スロットなど) に存在します。

1. **操作を繰り返す:** この時点でソース スロットには、スワップ以前にはターゲット スロットにあったアプリがあるため、すべての設定を適用し、ソース スロットのインスタンスを再起動して、同じ操作を完了します。

以下の点に注意してください。

- スワップ操作の任意の時点で、スワップされたアプリの初期化がソース スロットで行われます。 ソース スロットが準備されている間、ターゲット スロットはオンラインのままになります。スワップが成功するか失敗するかは関係ありません。

- ステージング スロットを運用スロットとスワップする場合は、運用スロットが "*常に*" ターゲット スロットであるようにします。 こうすることで、スワップ操作が運用アプリに影響を及ぼしません。

- "*スワップを開始する前*" に、イベント ソースおよびバインドに関連する設定を [デプロイ スロット設定](#manage-settings)として構成する必要があります。 事前に "固定" としてマークすることで、確実にイベントと出力が適切なインスタンスに送信されるようになります。

## <a name="manage-settings"></a>設定の管理

構成設定には、スロット固有のものがあります。 スロットをスワップしたときに変更される設定と変更されない設定を次の一覧に示します。

**スロット固有の設定**:

* 発行エンドポイント
* カスタム ドメイン名
* パブリックでない証明書と TLS/SSL 設定
* スケールの設定
* Web ジョブ スケジューラ
* IP 制限
* 常時接続
* 診断設定
* クロスオリジン リソース共有 (CORS)

**スロット固有ではない設定**:

* 一般設定 (フレームワーク バージョン、32/64 ビット、Web ソケットなど)
* アプリ設定 (スロット固有として構成可能)
* 接続文字列 (スロット固有として構成可能)
* ハンドラー マッピング
* パブリック証明書
* Web ジョブ コンテンツ
* ハイブリッド接続 *
* Virtual Network 統合 *
* サービス エンドポイント *
* Azure Content Delivery Network *

アスタリスク (*) 記号付きの機能は、スワップされない予定です。 

> [!NOTE]
> スワップされていない設定に適用されている特定のアプリ設定もスワップされません。 たとえば、診断の設定はスワップされないため、`WEBSITE_HTTPLOGGING_RETENTION_DAYS` や `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` などの関連するアプリ設定もスワップされません。これは、スロット設定として表示されない場合でも同様です。
>

### <a name="create-a-deployment-setting"></a>デプロイ設定を作成する

設定をデプロイ設定としてマークし、"固定" にすることができます。 固定設定は、アプリ インスタンスとはスワップされません。

1 つのスロットにデプロイ設定を作成する場合は、必ず、スワップに関係する他のすべてのスロットに一意の値を持つ同じ設定を作成してください。 このようにすると、設定の値は変更されませんが、設定の名前はスロット間で一貫性が保たれます。 この名前の一貫性により、あるスロットでは定義されているが、別のものでは定義されていない設定へのアクセスがコードで試行されなくなります。

デプロイ設定を作成するには、次の手順を使用します。

1. 関数アプリで **デプロイ スロット** に移動し、スロット名を選択します。

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Azure portal でスロットを検索します。" border="true":::

1. **[構成]** を選択し、現在のスロットで固定する設定名を選択します。

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Azure portal でスロットのアプリケーション設定を構成します。" border="true":::

1. **[デプロイ スロットの設定]** を選択し、 **[OK]** を選択します。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="デプロイ スロットの設定を構成します。" border="true":::

1. [設定] セクションが表示されなくなったら、 **[保存]** を選択して変更を保持します。

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="デプロイ スロットの設定を保存します。" border="true":::

## <a name="deployment"></a>デプロイ

スロットの作成時は、スロットは空になっています。 [サポートされているデプロイ テクノロジ](./functions-deployment-technologies.md)のいずれかを使用して、アプリケーションをスロットにデプロイできます。

## <a name="scaling"></a>Scaling

すべてのスロットが、運用スロットと同じ数のワーカーにスケーリングされます。

- 従量課金プランでは、関数アプリがスケーリングされると、スロットがスケーリングされます。
- App Service プランの場合、アプリは一定数のワーカーにスケーリングされます。 スロットは、アプリ プランと同じ数のワーカーで実行されます。

## <a name="add-a-slot"></a>スロットを追加する

スロットは、[CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create) を介して、またはポータルを使用して追加できます。 次の手順では、ポータルで新しいスロットを作成する方法を示します。

1. 関数アプリに移動します。

1. **[デプロイ スロット]** を選択し、 **[+ スロットの追加]** を選択します。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Azure Functions デプロイ スロットを追加します。" border="true":::

1. スロットの名前を入力して、 **[追加]** を選択します。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Azure Functions デプロイ スロットに名前を付けます。" border="true":::

## <a name="swap-slots"></a>スロットをスワップする

スロットは、[CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap) を介して、またはポータルを使用してスワップできます。 次の手順では、ポータルでスロットをスワップする方法を示します。

1. 関数アプリに移動します。
1. **[デプロイ スロット]** を選択し、 **[スワップ]** を選択します。

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="[スロットの追加] アクションが選択されている [デプロイ スロット] ページを示すスクリーンショット。" border="true":::

1. スワップの構成設定を確認して、 **[スワップ]** を選択します。
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="デプロイ スロットをスワップします。" border="true":::

スワップ操作の実行中は、操作にしばらく時間がかかる場合があります。

## <a name="roll-back-a-swap"></a>スワップをロールバックする

スワップによってエラーが発生した場合、またはスワップを "元に戻す" だけの場合は、初期状態にロールバックすることができます。 スワップ前の状態に戻るには、別のスワップを実行してスワップを逆にします。

## <a name="remove-a-slot"></a>スロットを削除する

スロットは、[CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete) を介して、またはポータルを使用して削除できます。 次の手順では、ポータルでスロットを削除する方法を示します。

1. 関数アプリで **デプロイ スロット** に移動し、スロット名を選択します。

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Azure portal でスロットを検索します。" border="true":::

1. **[削除]** を選択します。

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="[削除] アクションが選択されている [概要] ページを示すスクリーンショット。" border="true":::

1. 削除するデプロイ スロットの名前を入力し、 **[削除]** を選択します。

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Azure portal でデプロイ スロットを削除します。" border="true":::

1. [削除の確認] ウィンドウを閉じます。

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="デプロイ スロットの削除の確認。" border="true":::

## <a name="automate-slot-management"></a>スロット管理を自動化する

[Azure CLI](/cli/azure/functionapp/deployment/slot) を使用すると、スロットに対する次の操作を自動化できます。

- [create](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create)
- [delete](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete)
- [list](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-list)
- [スワップ](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap)
- [自動スワップ](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>App Service プランを変更する

App Service プランで実行されている関数アプリでは、スロットの基になる App Service プランを変更することができます。

> [!NOTE]
> 従量課金プランでは、スロットの App Service プランを変更することはできません。

スロットの App Service プランを変更するには、次の手順を使用します。

1. 関数アプリで **デプロイ スロット** に移動し、スロット名を選択します。

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Azure portal でスロットを検索します。" border="true":::

1. **[App Service プラン]** で、 **[App Service プランの変更]** を選択します。

1. アップグレードするプランを選択するか、新しいプランを作成します。

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Azure portal で App Service プランを変更します。" border="true":::

1. **[OK]** を選択します。

## <a name="limitations"></a>制限事項

Azure Functions デプロイ スロットには、次の制限があります。

- アプリで使用できるスロットの数は、プランによって異なります。 従量課金プランでは 1 つのデプロイ スロットのみが許可されます。 App Service プランで実行されているアプリでは、追加のスロットを使用できます。
- スロットをスワップすると、`AzureWebJobsSecretStorageType` アプリ設定が `files` に等しいアプリのキーがリセットされます。
- スロットは、Linux 従量課金プランでは使用できません。

## <a name="support-levels"></a>サポート レベル

デプロイ スロットには、次の 2 つのレベルのサポートがあります。

- **一般公開 (GA)** :完全にサポートされ、運用環境用に承認されています。
- **プレビュー**:まだサポートされていませんが、今後 GA 状態に達すると想定されています。

| OS/ホスティング プラン           | サポートのレベル     |
| ------------------------- | -------------------- |
| Windows Consumption       | 一般公開 |
| Windows Premium           | 一般公開  |
| Windows Dedicated         | 一般公開 |
| Linux Consumption         | サポートされていない          |
| Linux Premium             | 一般公開  |
| Linux Dedicated           | 一般公開 |

## <a name="next-steps"></a>次のステップ

- [Azure Functions のデプロイ テクノロジ](./functions-deployment-technologies.md)
