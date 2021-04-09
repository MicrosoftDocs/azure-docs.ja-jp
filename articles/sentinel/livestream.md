---
title: Azure Sentinel でハンティング ライブストリームを使用して脅威を検出する | Microsoft Docs
description: この記事では、Azure Sentinel でハンティング ライブストリームを使用してデータを追跡する方法を説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2020
ms.author: yelevin
ms.openlocfilehash: 14928d3c94ced8d1cd0c12e76428be73b68b91d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84783165"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Azure Sentinel でハンティング ライブストリームを使用して脅威を検出する

ハンティング ライブストリームを使用して、イベントの発生時に新たに作成したクエリをテストしたり、一致が見つかった場合にセッションから通知を取得したり、必要に応じて調査を開始したりできるようにする対話型セッションを作成します。 どのような Log Analytics クエリを使用したライブストリーム セッションでも、すばやく作成できます。

- **イベントの発生時に新たに作成したクエリをテストする**
    
    イベントにアクティブに適用されている現在のルールとの競合を発生させずに、クエリをテストし、調整することができます。 これらの新しいクエリが期待どおりに動作することを確認したら、セッションをアラートに昇格させるオプションを選択することで、クエリをカスタムのアラート ルールに簡単に昇格させることができます。

- **脅威が発生した際に通知を受け取る**
    
    脅威データ フィードを集計済みのログ データと比較し、一致が見つかった場合に通知を受け取ることができます。 脅威データ フィードは、潜在的脅威または現在の脅威に関連する継続的なデータ ストリームであるため、この通知から組織に対する潜在的脅威が判明する可能性があります。 カスタムのアラート ルールを維持するオーバーヘッドを発生させることなく潜在的な問題についての通知を受け取りたい場合には、カスタムのアラート ルールの代わりにライブストリーム セッションを作成します。

- **調査を開始する**
    
    ホストやユーザーなどの資産に関連するアクティブな調査を行っている場合、特定の (または任意の) アクティビティがその資産で発生したときにログ データ内でそれを確認できます。 そのアクティビティが発生したときに、通知を受け取ることもできます。


## <a name="create-a-livestream-session"></a>ライブストリーム セッションを作成する

既存のハンティング クエリからライブストリーム セッションを作成することも、ゼロからセッションを作成することもできます。

1. Azure portal で、 **[Sentinel]**  >  **[脅威の管理]**  >  **[ハンティング]** の順に移動します。

1. ハンティング クエリからライブストリーム セッションを作成するには、次のようにします。
    
    1. **[クエリ]** タブで、使用するハンティング クエリを見つけます。
    1. クエリを右クリックし、 **[Add to Livestream]\(ライブストリームに追加\)** を選択します。 次に例を示します。
    
    > [!div class="mx-imgBorder"]
    > ![Azure Sentinel ハンティング クエリからライブストリーム セッションを作成する](./media/livestream/livestream-from-query.png)

1. ゼロからライブストリーム セッションを作成するには、次のようにします。 
    
    1. **[Livestream]\(ライブストリーム\)** タブを選択します
    1. **[+ 新しいライブストリーム]** をクリックします。
    
1. **[Livestream]\(ライブストリーム\)** ウィンドウで、次を実行します。
    
    - クエリからライブストリームを開始した場合は、クエリを確認し、必要があれば変更を加えてください。
    - ゼロからライブストリームの作成を開始した場合は、クエリを作成します。 

1. コマンド バーで、 **[再生]** を選択します。
    
    コマンド バーの下にあるステータス バーに、ライブストリーム セッションが実行中であるか一時停止しているかが示されます。 次の例では、セッションが実行中です。
    
    > [!div class="mx-imgBorder"]
    > ![Azure Sentinel ハンティングからライブストリーム セッションを作成する](./media/livestream/livestream-session.png)

1. コマンド バーで、 **[保存]** を選択します。
    
    **[一時停止]** を選択しない限り、Azure portal からサインアウトするまで、セッションの実行が継続されます。

## <a name="view-your-livestream-sessions"></a>ライブストリーム セッションを表示する

1. Azure portal で、 **[Sentinel]**  >  **[脅威の管理]**  >  **[Hunting]\(ハンティング\)**  >  **[Livestream]\(ライブストリーム\)** タブの順に移動します。

1. 表示または編集するライブストリーム セッションを選択します。 次に例を示します。
    
    > [!div class="mx-imgBorder"]
    > ![Azure Sentinel ハンティング クエリからライブストリーム セッションを作成する](./media/livestream/livestream-tab.png)
    
    選択したライブストリーム セッションが開き、再生、一時停止、編集などを行うことができるようになります。

## <a name="receive-notifications-when-new-events-occur"></a>新しいイベントが発生したときに通知を受け取る

新しいイベントについてのライブストリーム通知では Azure portal の通知が使用されるため、Azure portal を使用すればいつでもこれらの通知を確認できます。 次に例を示します。

![Azure portal のライブストリーム通知](./media/livestream/notification.png)

通知を選択すると、 **[Livestream]\(ライブストリーム\)** ウィンドウが開きます。
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>ライブストリーム セッションをアラートに昇格させる

ライブストリーム セッションのコマンド バーから **[アラートに昇格]** を選択することで、そのライブストリーム セッションを新しいアラートに昇格させることができます。

> [!div class="mx-imgBorder"]
> ![ライブストリーム セッションをアラートに昇格させる](./media/livestream/elevate-to-alert.png)

このアクションにより、ルールの作成ウィザードが開きます。このウィザードには、ライブストリーム セッションに関連付けられているクエリが事前に設定されています。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Sentinel でハンティング ライブストリームを使用する方法を説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [脅威を事前に検出する](hunting.md)
- [ノートブックを使用して自動化された検出キャンペーンを実行する](notebooks.md)
