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
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 73a6d0eaab286dec9d02bb55eb75f0781bcffcc4
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891585"
---
# <a name="notification-hubs-security"></a>Notification Hubs のセキュリティ

## <a name="overview"></a>概要

このトピックでは、Azure Notification Hubs のセキュリティ モデルについて説明します。

## <a name="shared-access-signature-security-sas"></a>Shared Access Signature セキュリティ (SAS)

Notification Hubs は、SAS (Shared Access Signature) と呼ばれるエンティティ レベルのセキュリティ方式を実装しています。 各規則には、名前、キーの値 (共有シークレット)、および「[セキュリティ要求](#security-claims)」で説明されている権限のセットが含まれます。 通知ハブを作成すると、2 つの規則が自動的に作成されます。1 つは **Listen** 権限を持ち (クライアント アプリが使用するもの)、もう 1 つは**すべて**の権限を持ちます (アプリ バックエンドが使用するもの)。

クライアント アプリから登録管理を実行するとき、通知で送信される情報が機密性のものではない場合の (たとえば、最新の気象情報)、Notification Hub への一般的なアクセス方法は、クライアント アプリには Listen のみのアクセス規則のキー値を渡し、アプリ バックエンドにはフル アクセス規則のキー値を渡すというものです。

アプリでは、Windows ストア クライアント アプリにキー値を埋め込むことはできません。代わりに、起動時にクライアント アプリがアプリのバックエンドから取得するようにします。

**Listen** アクセスによるキーでは、クライアント アプリを任意のタグに登録できます。 アプリにおいて特定のタグへの登録を特定のクライアントに制限しなければならない場合は (たとえば、タグがユーザー ID を表す場合)、アプリ バックエンドが登録を実行する必要があります。 詳しくは、「[登録管理](notification-hubs-push-notification-registration-management.md)」をご覧ください。 この方法ではクライアント アプリは Notification Hubs に直接アクセスしないことに注意してください。

## <a name="security-claims"></a>セキュリティ要求

他のエンティティと同様に、通知ハブの操作は 3 つのセキュリティ要求**Listen**、**Send**、および **Manage** に対して許可されています。

| 要求   | 説明                                          | 許可される操作 |
| ------- | ---------------------------------------------------- | ------------------ |
| Listen  | 単一の登録の作成/更新、読み取り、削除を行います | 登録を作成/更新する<br><br>登録を読み取る<br><br>ハンドルのすべての登録を読み取る<br><br>登録を削除する |
| Send    | Notification Hubs にメッセージを送信します                | メッセージを送信する |
| 管理  | Notification Hubs に対する CRUD (PNS 資格情報およびセキュリティ キーの更新を含む) およびタグに基づく登録の読み取りを行います |Notification Hubs を作成/更新/読み取り/削除する<br><br>タグで登録を読み取る |

Notification Hubs では、通知ハブに直接構成された共有キーを使用して生成された署名トークンを受け付けます。

複数の名前空間に通知を送信することはできません。 名前空間は通知ハブの論理コンテナーであり、通知の送信に関係しません。
名前空間レベルのアクセス ポリシー (資格情報) を名前空間レベルの操作で使用できます。これには、通知ハブの一覧表示、通知ハブの作成または削除などが含まれます。通知を送信できるのは、ハブレベルのアクセス ポリシーを使用する場合のみです。
