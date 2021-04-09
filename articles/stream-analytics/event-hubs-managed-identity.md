---
title: Azure Stream Analytics ジョブからマネージド ID を使用してイベント ハブにアクセスする (プレビュー)
description: この記事では、マネージド ID を使用して、Azure Event Hubs の入力と出力に対して Azure Stream Analytics ジョブの認証を行う方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 4dc718f21f2ef3beabc31821bd60b571ac07be05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018753"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Azure Stream Analytics ジョブからマネージド ID を使用してイベント ハブにアクセスする (プレビュー)

Azure Stream Analytics では、Azure Event Hubs の入力と出力の両方に対してマネージド ID 認証がサポートされています。 マネージド ID を使用すると、パスワードの変更や 90 日ごとに発生するユーザー トークンの有効期限切れによる再認証の必要性など、ユーザー ベースの認証方法の制限がなくなります。 手動による認証の必要がなくなると、Stream Analytics のデプロイを完全に自動化できます。  

マネージド ID は、特定の Stream Analytics ジョブを表す、Azure Active Directory に登録済みのマネージド アプリケーションです。 ファイアウォールや仮想ネットワーク (VNet) の背後にある Event Hubs など、対象のリソースに対して認証を行うためにマネージド アプリケーションが使用されます。 ファイアウォールをバイパスする方法の詳細については、「[プライベート エンドポイント経由での Azure Event Hubs 名前空間へのアクセスを許可する](../event-hubs/private-link-service.md#trusted-microsoft-services)」を参照してください。

この記事では、Azure portal を使用して、Stream Analytics ジョブの Event Hubs の入力または出力に対してマネージド ID を有効にする方法を示します。 マネージド ID を有効にする前に、まず Stream Analytics ジョブと イベント ハブ リソースを用意する必要があります。

### <a name="limitation"></a>制限事項
プレビュー期間中にマネージド ID 認証モードを使用すると、Azure portal にある Event Hubs からのサンプリング入力が機能しなくなります。

## <a name="create-a-managedidentity"></a>マネージド ID を作成する  

まず、Azure Stream Analytics ジョブに対するマネージド ID を作成します。  

1. Azure portal で、Azure Stream Analytics ジョブを開きます。  

1. 左側のナビゲーション メニューから、 *[構成]* の下にある  **[マネージド ID]**   を選択します。 次に、 **[システム割り当てマネージド ID を使用]**   のチェック ボックスをオンにして、 **[保存]** を選択します。

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="システム割り当てマネージド ID":::  

1. Azure Active Directory に Stream Analytics ジョブの ID 用のサービス プリンシパルが作成されます。 新しく作成された ID のライフ サイクルは、Azure によって管理されます。 Stream Analytics ジョブが削除されると、関連付けられた ID (つまりサービス プリンシパル) も Azure によって自動的に削除されます。  

   構成を保存すると、サービス プリンシパルのオブジェクト ID (OID) が、次に示すようにプリンシパル ID として表示されます。  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="プリンシパル ID":::

   サービス プリンシパルは、Stream Analytics ジョブと同じ名前を持ちます。 たとえば、ジョブの名前が  `MyASAJob` であれば、サービス プリンシパルの名前も  `MyASAJob` になります。  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>イベント ハブにアクセスするためのアクセス許可を Stream Analytics ジョブに付与する

Stream Analytics ジョブでマネージド ID を使用してイベント ハブにアクセスするには、作成したサービス プリンシパルにそのイベント ハブに対する特別なアクセス許可が必要です。

1. イベント ハブの **[アクセス制御 (IAM)]** に移動します。

1. **[+ 追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。

1. **[ロールの割り当ての追加]** ページで、次のオプションを入力します。

   |パラメーター|値|
   |---------|-----|
   |Role|Azure Event Hubs データ所有者|
   |アクセスの割り当て先|ユーザー、グループ、またはサービス プリンシパル|
   |選択|Stream Analytics ジョブの名前を入力する|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="ロールの割り当てを追加する":::

1. **[保存]** を選択し、変更が伝搬されるまで 1 分ほど待機します。

また、このロールをイベント ハブ名前空間レベルで付与することもできます。これにより、その下に作成されたすべてのイベント ハブにアクセス許可が自然に伝搬されます。 つまり、名前空間の下にあるすべてのイベント ハブを、Stream Analytics ジョブでマネージド ID 認証リソースとして使用できます。

## <a name="create-anevent-hub-input-or-output"></a>イベント ハブの入力または出力を作成する  

マネージド ID を構成したので、イベント ハブ リソースを入力または出力として Stream Analytics ジョブに追加する準備ができました。  

### <a name="add-the-event-hub-as-an-input"></a>イベント ハブを入力として追加する 

1. Stream Analytics ジョブに移動し、 **[ジョブ トポロジ]** の下にある **[入力]** ページに移動します。

1. **[ストリーム入力の追加] > [イベント ハブ]** を選択します。 入力プロパティ ウィンドウで、お使いのイベント ハブを検索して選択し、 *[認証モード]* ドロップダウン メニューから **[マネージド ID]** を選択します。

1. 残りのプロパティを入力し、 **[保存]** を選択します。

### <a name="add-the-event-hub-as-an-output"></a>イベント ハブを出力として追加する

1. Stream Analytics ジョブに移動し、 **[ジョブ トポロジ]** の下にある **[出力]** ページに移動します。

1. **[追加] > [イベント ハブ]** を選択します。 出力プロパティ ウィンドウで、お使いのイベント ハブを検索して選択し、 *[認証モード]* ドロップダウン メニューから **[マネージド ID]** を選択します。

1. 残りのプロパティを入力し、 **[保存]** を選択します。

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics からの Event Hubs 出力](event-hubs-output.md)
* [Event Hubs からのデータのストリーム配信](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
