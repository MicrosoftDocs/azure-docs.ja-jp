---
title: Notification Hubs のセキュリティ
description: このトピックでは、Azure Notification Hubs のセキュリティについて説明します。
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bd9df12cbe941b868c769daccd02c1d81b39f7bd
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465362"
---
# <a name="security-model-of-azure-notification-hubs"></a>Azure Notification Hubs のセキュリティ モデル

## <a name="overview"></a>概要

このトピックでは、Azure Notification Hubs のセキュリティ モデルについて説明します。 Notification Hubs は Service Bus エンティティなので、Service Bus と同じセキュリティ モデルを実装しています。 詳細については、「 [Service Bus Authentication （Service Bus の認証）](https://msdn.microsoft.com/library/azure/dn155925.aspx) 」のトピックを参照してください。

## <a name="shared-access-signature-security-sas"></a>Shared Access Signature セキュリティ (SAS)

Notification Hubs は、SAS (Shared Access Signature) と呼ばれるエンティティ レベルのセキュリティ方式を実装しています。 この方式では、メッセージング エンティティは最大 12 個の承認規則を記述で宣言し、そのエンティティに対する権限を付与できます。

各規則には、名前、キーの値 (共有シークレット)、および「セキュリティ要求」セクションで説明されている権限のセットが含まれます。 Notification Hub を作成すると、2 つの規則が自動的に作成されます。1 つは Listen 権限を持ち (クライアント アプリが使用するもの)、もう 1 つはすべてのすべての権限を持ちます (アプリ バックエンドが使用するもの)。

クライアント アプリから登録管理を実行するとき、通知で送信される情報が機密性のものではない場合の (たとえば、最新の気象情報)、Notification Hub への一般的なアクセス方法は、クライアント アプリには Listen のみのアクセス規則のキー値を渡し、アプリ バックエンドにはフル アクセス規則のキー値を渡すというものです。

Windows ストア クライアント アプリにキーの値を埋め込むことは推奨されません。 キー値を埋め込まなくて済むようにするには、クライアント アプリの起動時にアプリ バックエンドからキーを取得します。

Listen アクセス権を持つキーを使用するとクライアント アプリは任意のタグに登録できるということを理解しておく必要があります。 アプリで特定のタグへの登録を特定のクライアントに制限する必要がある場合は (たとえば、タグがユーザー ID を表す場合)、アプリ バックエンドが登録を実行する必要があります。 詳細については、「登録管理」を参照してください。 この方法ではクライアント アプリは Notification Hubs に直接アクセスしないことに注意してください。

## <a name="security-claims"></a>セキュリティ要求

他のエンティティと同様に、通知ハブの操作は 3 つのセキュリティ要求Listen、Send、Manage に対して許可されています。

| 要求   | 説明                                          | 許可される操作 |
| ------- | ---------------------------------------------------- | ------------------ |
| Listen  | 単一の登録の作成/更新、読み取り、削除を行います | 登録を作成/更新する<br><br>登録を読み取る<br><br>ハンドルのすべての登録を読み取る<br><br>登録を削除する |
| Send    | Notification Hubs にメッセージを送信します                | メッセージを送信する |
| 管理  | Notification Hubs に対する CRUD (PNS 資格情報およびセキュリティ キーの更新を含む) およびタグに基づく登録の読み取りを行います |Notification Hubs を作成/更新/読み取り/削除する<br><br>タグで登録を読み取る |

Notification Hubs は、Microsoft Azure Access Control トークンによって許可された要求、および Notification Hubs に直接構成された共有キーを使用して生成された署名トークンによって許可された要求を受け付けます。
