---
title: Azure Service Bus - メッセージング エンティティを一時停止する
description: この記事では、Azure Service Bus メッセージ エンティティ (キュー、トピック、およびサブスクリプション) を一時停止し、再アクティブ化する方法について説明します。
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94543053"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>メッセージ エンティティの一時停止と再アクティブ化 (無効化)

キュー、トピック、およびサブスクリプションは、一時停止することができます。 一時停止されたエンティティは無効な状態になり、すべてのメッセージがストレージに保持されます。 ただし、メッセージを削除または追加することはできず、各プロトコル操作はエラーになります。

緊急の管理上の理由で、エンティティを中断することをお勧めします。 たとえば、キューからメッセージを取る受信プログラムに欠陥があり、処理に失敗したにもかかわらず正しくない状態でメッセージが完了するため、それらが削除されるとします。 この場合は、コードを修正してデプロイするまで、受信用のキューを無効にすることをお勧めします。 

一時停止または再アクティブ化はユーザーまたはシステムのいずれかが実行できます。 サブスクリプションの使用制限を越えるなど重大な管理上の理由がある場合のみ、システムによってエンティティが一時停止されます。 システムによって無効化されたエンティティは、ユーザーによって再アクティブ化することはできませんが、一時停止の原因が解消されれば復元されます。

## <a name="queue-status"></a>キューの状態 
**キュー** に設定可能な状態は次のとおりです。

-   **Active**: キューはアクティブです。 キューとの間でメッセージを送信したり、メッセージを受信したりすることができます。 
-   **Disabled**: キューは一時停止状態です。 これは、**SendDisabled** と **ReceiveDisabled** の両方を設定することと同じです。 
-   **SendDisabled**: キューとの間で、メッセージを送信することはできませんが、メッセージを受信することはできます。 キューにメッセージを送信しようとすると、例外が発生します。 
-   **ReceiveDisabled**: キューとの間で、メッセージを送信することはできますが、メッセージを受信することはできません。 キューからメッセージを受信しようとすると、例外が発生します。


### <a name="change-the-queue-status-in-the-azure-portal"></a>Azure portal でキューの状態を変更します。 

1. Azure portal で、使用する Service Bus 名前空間に移動します。 
1. 状態を変更するキューを選択します。 下部のペインの中央にキューが表示されます。 
1. **[Service Bus キュー]** ページに、キューの現在の状態がハイパーリンクとして表示されます。 左側のメニューで **[概要]** を選択していない場合は、それを選択してキューの状態を表示します。 キューの現在の状態を変更するには、それを選択します。 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="キューの状態を選択する":::
4. キューの新しい状態を選択して、 **[OK]** を選択します。 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="キューの状態を設定する":::
    
また、.NET SDK の Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API を使用するか、Azure CLI または Azure PowerShell 経由の Azure Resource Manager テンプレートを使用することで、送信および受信の操作を無効化することもできます。

### <a name="change-the-queue-status-using-azure-powershell"></a>Azure PowerShell を使用してキューの状態を変更する
キューを無効にする PowerShell コマンドの例を次に示します。 再アクティブ化コマンドは同様ですが、`Status` が **Active** に設定されます。

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>トピックの状態
トピックの状態は、Azure portal で変更できます。 トピックの現在の状態を選択すると、次のページが表示され、状態を変更できるようになります。 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="トピックの状態を変更する":::

**トピック** に設定できる状態は次のとおりです。
- **[アクティブ]** : トピックはアクティブです。 トピックにメッセージを送信することができます。 
- **Disabled**: トピックは中断されています。 トピックにメッセージを送信することはできません。 
- **SendDisabled**: **Disabled** と同じ効果があります。 トピックにメッセージを送信することはできません。 トピックにメッセージを送信しようとすると、例外が発生します。 

## <a name="subscription-status"></a>サブスクリプションの状態
サブスクリプションの状態は、Azure portal で変更できます。 サブスクリプションの現在の状態を選択すると、次のページが表示され、状態を変更できるようになります。 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="サブスクリプションの状態を変更する":::

**サブスクリプション** に設定できる状態は次のとおりです。
- **[アクティブ]** : サブスクリプションがアクティブである。 サブスクリプションからメッセージを受信することができます。
- **Disabled**: サブスクリプションが中断されました。 サブスクリプションからメッセージを受信することはできません。 
- **ReceiveDisabled**: **Disabled** と同じ効果があります。 サブスクリプションからメッセージを受信することはできません。 サブスクリプションからメッセージを受信しようとすると、例外が発生します。

| トピックの状態 | サブスクリプションの状態 | 動作 | 
| ------------ | ------------------- | -------- | 
| アクティブ | アクティブ | トピックへのメッセージの送信、およびサブスクリプションからのメッセージの受信を行うことができます。 | 
| アクティブ | 無効または受信は無効 | トピックにメッセージを送信することはできますが、サブスクリプションからメッセージを受信することはできません。 | 
| 無効または送信は無効 | アクティブ | トピックにメッセージを送信することはできませんが、サブスクリプションに既に存在するメッセージを受信することはできます。 | 
| 無効または送信は無効 | 無効または受信は無効 | トピックにメッセージを送信することはできません。サブスクリプションから受信することもできません。 | 

## <a name="other-statuses"></a>その他の状態
[EntityStatu](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 列挙型は、システムによってのみ設定可能な一連の遷移状態も定義します。 


## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

