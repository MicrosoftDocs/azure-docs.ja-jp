---
title: Notification Hubs のセキュリティ モデル
description: Azure Notification Hubs のセキュリティ モデルについて説明します。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 07600b1fe0cb7420989fbbfbe55c2f1a4197d2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548252"
---
# <a name="notification-hubs-security"></a>Notification Hubs のセキュリティ

## <a name="overview"></a>概要

このトピックでは、Azure Notification Hubs のセキュリティ モデルについて説明します。

## <a name="shared-access-signature-security"></a>Shared Access Signature セキュリティ

Notification Hubs では、*Shared Access Signature* (SAS) と呼ばれるエンティティ レベルのセキュリティ方式が実装されています。 各規則には、名前、キーの値 (共有シークレット)、および権限のセット (「[セキュリティ要求](#security-claims)」で後述) が含まれます。 

ハブを作成すると、2 つの規則が自動的に作成されます。1 つには **Listen** 権限が含まれ (クライアント アプリによって使用されます)、もう 1 つには **すべて** の権限が含まれます (アプリのバックエンドによって使用されます)。

- **DefaultListenSharedAccessSignature**: **Listen** アクセス許可のみを付与します。
- **DefaultFullSharedAccessSignature**: **Listen**、**Manage**、および **Send** の各アクセス許可を付与します。 このポリシーは、アプリのバックエンドでのみ使用してください。 クライアント アプリケーションでは、これを使用せずに、**Listen** アクセス権のみを持つポリシーを使用してください。 新しい SAS トークンを使用して新しいカスタム アクセス ポリシーを作成するには、この記事で後述する「[アクセス ポリシー用の SAS トークン](#sas-tokens-for-access-policies)」を参照してください。

クライアント アプリから登録管理を実行するとき、通知で送信される情報が機密性のものではない場合の (たとえば、最新の気象情報)、Notification Hub への一般的なアクセス方法は、クライアント アプリには Listen のみのアクセス規則のキー値を渡し、アプリ バックエンドにはフル アクセス規則のキー値を渡すというものです。

アプリに、Windows ストア クライアント アプリのキー値を埋め込まないでください。代わりに、クライアント アプリの起動時にアプリのバックエンドからそれを取得するようにしてください。

**Listen** アクセスによるキーでは、クライアント アプリを任意のタグに登録できます。 アプリにおいて特定のタグへの登録を特定のクライアントに制限しなければならない場合は (たとえば、タグがユーザー ID を表す場合)、アプリ バックエンドが登録を実行する必要があります。 詳しくは、「[登録管理](notification-hubs-push-notification-registration-management.md)」をご覧ください。 この方法ではクライアント アプリは Notification Hubs に直接アクセスしないことに注意してください。

## <a name="security-claims"></a>セキュリティ要求

他のエンティティと同様に、Notification Hub の操作は 3 つのセキュリティ要求 **Listen**、**Send** および **Manage** に対して許可されます。

| 要求   | 説明                                          | 許可される操作 |
| ------- | ---------------------------------------------------- | ------------------ |
| リッスン  | 単一の登録の作成/更新、読み取り、削除を行います | 登録を作成/更新する<br><br>登録を読み取る<br><br>ハンドルのすべての登録を読み取る<br><br>登録の削除 |
| 送信    | 通知ハブにメッセージを送信する                | メッセージを送信する |
| 管理する  | Notification Hubs に対する CRUD (PNS 資格情報およびセキュリティ キーの更新を含む) およびタグに基づく登録の読み取りを行います |ハブの作成/更新/読み取り/削除を行う<br><br>タグで登録を読み取る |

Notification Hubs では、ハブに直接構成された共有キーを使用して生成された SAS トークンが受け入れられます。

複数の名前空間に通知を送信することはできません。 名前空間は Notification Hubs の論理コンテナーであり、通知の送信には関係しません。

名前空間レベルの操作 (例: ハブの一覧表示、ハブの作成または削除、その他) では、名前空間レベルのアクセス ポリシー (資格情報) を使用します。ハブレベルのアクセス ポリシーを使用する場合のみ、通知を送信できます。

### <a name="sas-tokens-for-access-policies"></a>アクセス ポリシー用の SAS トークン

新しいセキュリティ要求を作成するか、既存の SAS キーを表示するには、次の手順を実行します。

1. Azure portal にサインインします。
2. **[すべてのリソース]** を選択します。
3. 要求を作成する、または SAS キーを表示する通知ハブの名前を選択します。
4. 左側のメニューで、**[アクセス ポリシー]** を選択します。
5. 新しいセキュリティ要求を作成するには、**[新しいポリシー]** を選択します。 ポリシーに名前を付け、付与するアクセス許可を選択します。 次に、 **[OK]** を選択します。
6. [アクセス ポリシー] ウィンドウに、(新しい SAS キーを含む) 完全な接続文字列が表示されます。 後で使用するために、この文字列をクリップボードにコピーできます。

特定のポリシーから SAS キーを抽出するには、目的の SAS キーが含まれているポリシーの横にある **[コピー]** ボタンを選択します。 この値を一時的な場所に貼り付けてから、接続文字列の SAS キー部分をコピーします。 この例では、**mytestnamespace1** という名前の Notification Hubs 名前空間と、**policy2** という名前のポリシーが使用されます。 SAS キーは、文字列の末尾付近にある値であり、**SharedAccessKey** によって指定されます。

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![SAS キーを取得する](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>次のステップ

- [Notification Hubs の概要](notification-hubs-push-notification-overview.md)
