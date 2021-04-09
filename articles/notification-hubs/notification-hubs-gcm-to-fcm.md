---
title: Azure Notification Hubs および Google Firebase Cloud Messaging (FCM) の移行
description: Azure Notification Hubs が Google GCM から FCM への移行に対処する方法について説明します。
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80127022"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure Notification Hubs および Google Firebase Cloud Messaging の移行

## <a name="current-state"></a>現在の状態

Google が Google Cloud Messaging (GCM) から Firebase Cloud Messaging (FCM) への移行について発表したとき、マイクロソフトなどのプッシュ サービスは、変更に対応するように Android デバイスに通知を送信する方法を調整する必要がありました。

マイクロソフトはサービス バックエンドを更新し、API および SDK に対する更新プログラムを公開しました。 マイクロソフトの実装では、顧客への影響を最小限に抑えるため既存の GCM 通知スキーマとの互換性を維持することにしました。 つまり、現在、FCM レガシ モードで FCM を使用して Android デバイスに通知を送信しています。 最終的には、新機能やペイロード形式などの FCM の真のサポートを追加したいと考えています。 これは長期的な変更になり、現在の移行は、既存のアプリケーションおよび SDK との互換性の維持に焦点を置いています。 GCM と FCM SDK のどちらでも (マイクロソフトの SDK と共に) アプリで使用でき、通知が正しく送信されるようにしています。

最近、一部のお客様には Google から、GCM エンドポイントを通知に使用しているアプリについて警告するメールが送信されています。 これは単なる警告であり、何も中断されません。アプリの Android 通知は引き続き処理のために Google に送信され、Google はそれらを処理し続けます。 サービス構成で明示的に GCM エンドポイントを指定していたお客様の中には、非推奨のエンドポイントをまだ使用している場合がありました。 マイクロソフトは既にこの不一致を確認しており、Google がメールを送信したときの問題の解決に取り組んでいました。

その非推奨のエンドポイントを置き換え、修正プログラムがデプロイされています。

## <a name="going-forward"></a>今後の予定

Google の FCM FAQ には、何もする必要がないと記されています。 [FCM FAQ](https://developers.google.com/cloud-messaging/faq) で、Google は、「クライアント SDK および GCM トークンは無期限に機能し続けます。 ただし、FCM に移行しない限り、Android アプリで 最新バージョンの Google Play 開発者サービスを対象にすることはできなくなります」と述べています。

アプリで GCM ライブラリを使用している場合は、Google からの指示に従って、アプリで FCM ライブラリにアップグレードしてください。 マイクロソフトの SDK はどちらにも互換性があるので、(マイクロソフトの SDK バージョンを最新にしている限り) マイクロソフト側でのアプリに更新は必要はありません。

## <a name="questions-and-answers"></a>質問と回答

お客様からよく寄せられる質問に対する回答は次のとおりです。

**Q:** 互換性を維持するため、期限までにどのようなことを行う必要がありますか (Google の現在の期限は 5 月 29 日で、変更される可能性があります)。

**A:** 何もする必要はありません。 既存の GCM 通知スキーマとの互換性が維持されます。 GCM キーは、アプリケーションで使用されているすべての GCM SDK およびライブラリと同様に、通常どおり機能し続けます。

新しい機能を利用するために FCM SDK およびライブラリへのアップグレードを決定した場合でも、GCM キーは引き続き動作します。 FCM キーの使用への切り替えは可能ですが、新しい Firebase プロジェクトを作成するときには、必ず既存の GCM プロジェクトに Firebase を追加してください。 これにより、GCM SDK とライブラリを使用し続けている旧バージョンのアプリを実行している顧客との下位互換性が保証されます。

新しい FCM プロジェクトを作成していて、既存の GCM プロジェクトへアタッチしていない場合は、新しい FCM シークレットを使用して Notification Hubs を更新すると、新しい FCM キーに古い GCM プロジェクトへのリンクがないため、現在のアプリのインストールに通知をプッシュする機能が失われます。

**Q:** 古い GCM エンドポイントを使用していることに関するこのメールは、なぜ送られてきたのでしょうか。 どうすればよいですか。

**A:** 何もする必要はありません。 新しいエンドポイントへの移行中ですが、まもなく完了しますので、変更は必要ありません。 何も中断されません。1 つのエンドポイントが失われたために、Google から警告メッセージが送られただけです。

**Q:** 既存のユーザーを中断せずに、新しい FCM SDK およびライブラリに移行するにはどうすればよいですか。

A: いつでもアップグレードしてください。 Google からは、既存の GCM SDK およびライブラリの廃止に関する発表はまだありません。 既存のユーザーへのプッシュ通知を中断しないようにするには、既存の GCM プロジェクトに関連付ける新しい Firebase プロジェクトをいつ作成するか確認してください。 これにより、新しい Firebase シークレットは、GCM SDK およびライブラリと共に以前のバージョンのアプリを実行しているユーザーに対して、FCM SDK およびライブラリを使用したアプリの新しいユーザーと同様に機能します。

**Q:** いつ、自分の通知に新しい FCM の機能およびスキーマを使用できますか。

**A:** API および SDK への更新プログラムを公開したら、引き続き情報をお待ちください – 数か月後に何かお届けできると思います。
