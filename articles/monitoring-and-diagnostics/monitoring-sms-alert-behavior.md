---
title: "アクション グループの SMS アラート動作 | Microsoft Docs"
description: "SMS メッセージの形式と登録解除、再登録、ヘルプ要求の SMS メッセージに応答する方法について説明します。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 10f33898fb86bd2449994a153d99cb59dc6078d6
ms.contentlocale: ja-jp
ms.lasthandoff: 03/31/2017

---
# <a name="sms-alert-behavior-in-action-groups"></a>アクション グループの SMS アラート動作
## <a name="overview"></a>概要 ##
アクション グループを使用すると、受信者のリストを構成できます。 これらのグループはアクティビティ ログのアラートを定義するときに活用できます。これにより、アクティビティ ログのアラートがトリガーされるときに特定のアクション グループが通知されます。 サポートされているアラート メカニズムの 1 つは SMS です。アラートは双方向通信をサポートします。 ユーザーは、次のことを行うためにアラートに応答できます。

- **アラートの登録を解除する:** ユーザーはすべてのアクション グループまたは単一のアクション グループのすべての SMS アラートの登録を解除できます。  
- **アラートに再登録する:** ユーザーはすべてのアクション グループまたは単一のアクション グループのすべての SMS アラートに再登録できます。  
- **サポートを要求する:** ユーザーは SMS で追加情報を問い合わせることができます。 ユーザーはこの記事にリダイレクトされます。

この記事は、ユーザーの次のロケールに基づいて、SMS アラートの動作とユーザーが実行できる応答アクションについて説明します。

## <a name="usacanada-sms-behavior"></a>米国/カナダにおける SMS の動作
### <a name="receiving-an-sms-alert"></a>SMS アラートを受信する
アクション グループの一部として構成されている SMS の受信者は、アラートが発生したときに SMS を受け取ります。 SMS には次の情報が記載されています。
* このアラートの送信先のアクション グループの短い名前
- アラートのタイトル

### <a name="unsubscribing-from-sms-alerts-for-one-action-group"></a>1 つのアクション グループの SMS アラートの登録を解除する
"DISABLE &lt;アクション グループの短い名前&gt;" というキーワードが含まれるショートコード 20873 に応答することで、1 つのアクション グループのアラートの SMS の登録を解除できます。

例: "Azure" という短い名前を持つアクション グループのアラートの登録を解除するには、"DISABLE Azure" が含まれるショートコード 20873 に SMS を送信します。

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>すべてのアクション グループの SMS アラートの登録を解除する
次のいずれかのキーワードが含まれるショートコード 20873 に応答することで、すべてのアクション グループのすべての SMS アラートの登録を解除できます。
* STOP

例: すべてのアクション グループのすべての SMS アラートの登録を解除するには、"STOP" が含まれるショートコード 20873 に SMS を送信します。

>[!NOTE]
>SMS アラートの登録を解除したユーザーが新しいアクション グループに追加されると、その新しいアクション グループの SMS アラートを受け取りますが、以前のすべてのアクション グループの登録は解除されたままになります。
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>1 つのアクション グループの SMS アラートに再登録する
"Enable &lt;アクション グループの短い名前&gt;" というキーワードが含まれるショートコード 20873 に応答することで、1 つのアクション グループのアラートの SMS に再登録できます。

例: "Azure" という短い名前を持つアクション グループのアラートに再登録するには、"ENABLE Azure" が含まれるショートコード 20873 に SMS を送信します。

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>すべてのアクション グループの SMS アラートに再登録する
次のいずれかのキーワードが含まれるショートコード 20873 に応答することで、すべてのアクション グループのすべての SMS アラートに再登録できます。

* START

例: すべてのアクション グループのすべての SMS アラートの登録を解除するには、"START" が含まれるショートコード 20873 に SMS を送信します。

### <a name="requesting-help-via-sms"></a>SMS を使用してヘルプを要求する
次のキーワードが含まれるショートコード 20873 に応答することで、受け取った SMS に関する詳細情報を要求できます。
* HELP

この記事へのリンクが記載された応答がユーザーに送信されます。

## <a name="next-steps"></a>次のステップ
[アクティビティ ログのアラートの概要](monitoring-overview-alerts.md)を把握し、アラートを生成する方法について学習します  
[SMS レートの制限](monitoring-alerts-rate-limiting.md)について学習します  
[アクション グループ](monitoring-action-groups.md)の詳細について学習します

