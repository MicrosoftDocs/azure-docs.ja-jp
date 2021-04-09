---
title: AS2 メッセージの設定
description: Azure Logic Apps と Enterprise Integration Pack の組み合わせにおける AS2 送受信設定のためのリファレンス ガイド
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74793032"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack の組み合わせにおける AS2 メッセージ設定のためのリファレンス

このリファレンスでは、取引相手間で送受信されるメッセージの AS2 契約による処理方法を指定する目的で設定できるプロパティについて説明されています。 メッセージを交換するパートナーとの契約に基づいて、これらのプロパティを設定します。

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 受信設定

![[受信設定] を選択します](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| プロパティ | 必須 | 説明 |
|----------|----------|-------------|
| **メッセージ プロパティをオーバーライドします** | いいえ | 受信メッセージのプロパティをご自分のプロパティ設定でオーバーライドします。 |
| **メッセージに署名してください** | いいえ | すべての受信メッセージにデジタル署名する必要があるかどうかを指定します。 署名が必要な場合、**[証明書]** ボックスの一覧から、メッセージの署名の検証に使用する既存のゲスト パートナーの公開証明書を選択します。 証明書をお持ちではない場合、[証明書の追加](../logic-apps/logic-apps-enterprise-integration-certificates.md)に関するページで詳細をご覧ください。 |
| **メッセージは暗号化する必要があります** | いいえ | すべての受信メッセージを暗号化する必要があるかどうかを指定します。 暗号化されていないメッセージは拒否されます。 暗号化が必要な場合、**[証明書]** ボックスの一覧から、受信メッセージの暗号化解除に使用する既存のホスト パートナーのプライベート証明書を選択します。 証明書をお持ちではない場合、[証明書の追加](../logic-apps/logic-apps-enterprise-integration-certificates.md)に関するページで詳細をご覧ください。 |
| **メッセージは圧縮する必要があります** | いいえ | すべての受信メッセージを圧縮する必要があるかどうかを指定します。 圧縮されていないメッセージは拒否されます。 |
| **メッセージ ID の重複を許可しない** | いいえ | ID が重複するメッセージを許可するかどうかを指定します。 重複する ID を許可しない場合、チェック間の日数を選択します。 重複を保留にするかどうかも選択できます。 |
| **MDN テキスト** | いいえ | メッセージの送信者に送信する既定の Message Disposition Notification (MDN) を指定します。 |
| **MDN を送信する** | いいえ | 受信したメッセージに同期 MDN を送信するかどうかを指定します。  |
| **署名付き MDN を送信する** | いいえ | 受信したメッセージに署名付き MDN を送信するかどうかを指定します。 署名が必要な場合、**[MIC アルゴリズム]** の一覧から、メッセージの署名に使用するアルゴリズムを選択します。 |
| **非同期 MDN を送信する** | いいえ | MDN を非同期で送信するかどうかを指定します。 非同期 MDN を選択する場合、**[URL]** ボックスに、MDN を送信する場所の URL を指定します。 |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 送信設定

![[送信設定] を選択します](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| プロパティ | 必須 | 説明 |
|----------|----------|-------------|
| **メッセージの署名を有効にしてください** | いいえ | すべての送信メッセージにデジタル署名する必要があるかどうかを指定します。 署名が必要な場合、以下の値を選択します。 <p>- **[署名アルゴリズム]** の一覧から、メッセージの署名に使用するアルゴリズムを選択します。 <br>- **[証明書]** ボックスの一覧から、メッセージに署名するための既存のホスト パートナーのプライベート証明書を選択します。 証明書をお持ちではない場合、[証明書の追加](../logic-apps/logic-apps-enterprise-integration-certificates.md)に関するページで詳細をご覧ください。 |
| **メッセージの暗号化を有効にしてください** | いいえ | すべての送信メッセージを暗号化する必要があるかどうかを指定します。 暗号化が必要な場合、以下の値を選択します。 <p>- **[暗号化アルゴリズム]** ボックスの一覧から、メッセージの暗号化に使用するゲスト パートナーの公開証明書のアルゴリズムを選択します。 <br>- **[証明書]** ボックスの一覧から、送信メッセージの暗号化するための既存のゲスト パートナーのプライベート証明書を選択します。 証明書をお持ちではない場合、[証明書の追加](../logic-apps/logic-apps-enterprise-integration-certificates.md)に関するページで詳細をご覧ください。 |
| **メッセージの圧縮を有効にしてください** | いいえ | すべての送信メッセージを圧縮する必要があるかどうかを指定します。 |
| **HTTP のヘッダーを展開する** | いいえ | HTTP `content-type` ヘッダーを単一行に置きます。 |
| **MIME ヘッダーのファイル名を転送する** | いいえ | MIME ヘッダーにファイル名を含めるかどうかを指定します。 |
| **MDN を要求する** | いいえ | すべての送信メッセージについて Message Disposition Notification (MDN) を受信するかどうかを指定します。 |
| **署名付き MDN を要求する** | いいえ | すべての送信メッセージについて、署名付き MDN を受信するかどうかを指定します。 署名が必要な場合、**[MIC アルゴリズム]** の一覧から、メッセージの署名に使用するアルゴリズムを選択します。 |
| **非同期 MDN を要求する** | いいえ | MDN を非同期で受信するかどうかを指定します。 非同期 MDN を選択する場合、**[URL]** ボックスに、MDN を送信する場所の URL を指定します。 |
| **NRR を有効にする** | いいえ | Non-Repudiation Receipt (NRR) を必要とするかどうかを指定します。 この通信属性は、データが指定の宛先で受信されたことの証拠を提供します。 |
| **SHA2 アルゴリズム形式** | いいえ | 送信 AS2 メッセージまたは MDN に対してヘッダーに署名するための MIC アルゴリズム形式を指定します。 |
||||

## <a name="next-steps"></a>次のステップ

[AS2 メッセージの交換](../logic-apps/logic-apps-enterprise-integration-as2.md)
