---
title: Azure Front Door での Web アプリケーション ファイアウォールのポリシーの設定
description: Web アプリケーション ファイアウォール (WAF) について説明します。
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 8f51cb6944221416b098a9b953db417053155f1e
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849100"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Azure Front Door での Web アプリケーション ファイアウォールのポリシーの設定

Web アプリケーション ファイアウォール (WAF) ポリシーを使用すると、一連のカスタム規則とマネージド規則によって、Web アプリケーションへのアクセスを制御することができます。 WAF ポリシーの名前は一意である必要があります。 既存の名前を使おうとすると、検証エラーが発生します。 この記事で説明するように、複数のポリシー レベルの設定が、そのポリシーに対して指定されているすべての規則に適用されます。

## <a name="waf-state"></a>WAF の状態

Front Door 用の WAF ポリシーは、次の 2 つの状態のいずれかにすることができます。
- **有効:** ポリシーを有効にすると、WAF は受信した要求をアクティブに検査し、規則の定義に従って対応するアクションを実行します
- **無効:** ポリシーを無効にすると、WAF の検査は一時停止されます。 受信した要求は、WAF をバイパスし、Front Door のルーティングに基づいてバックエンドに送信されます。

## <a name="waf-mode"></a>WAF のモード

WAF ポリシーは、次の 2 つのモードで実行するように構成できます。

- **検出モード** 検出モードで実行されている WAF では、監視以外のアクションは実施されず、要求とそれに一致した WAF ルールが WAF ログに記録されます。 Front Door に対して診断ログを有効にします (Azure portal では、 **[診断]** セクションで実現できます)。

- **防止モード** 防止モードで実行するように構成された WAF は、要求が規則に一致すると指定されたアクションを実行します。 一致した要求は、WAF ログにも記録されます。

## <a name="waf-response-for-blocked-requests"></a>ブロックされた要求に対する WAF の応答

既定では、規則が一致したために WAF で要求がブロックされると、403 状態コードと "**要求はブロックされました**" というメッセージが返されます。 ログ記録のために参照文字列も返されます。

要求が WAF でブロックされた場合のカスタム応答の状態コードと応答メッセージを定義することができます。 次のカスタム状態コードがサポートされています。

- 200    OK
- 403    禁止
- 405    メソッドは許可されていません
- 406    受信不可
- 429    要求が多すぎます

カスタム応答状態コードと応答メッセージは、ポリシー レベルの設定です。 いったん構成すると、ブロックされたすべての要求が、同じカスタム応答状態と応答メッセージを受け取ります。

## <a name="uri-for-redirect-action"></a>リダイレクト アクションの URI

WAF ポリシーに含まれるいずれかの規則に対して**リダイレクト** アクションが選択されている場合は、要求をリダイレクトする URI を定義する必要があります。 このリダイレクト URI には有効な HTTP(S) サイトを指定する必要があり、構成すると、"リダイレクト" アクションを含む規則と一致したすべての要求が、指定したサイトにリダイレクトされます。


## <a name="next-steps"></a>次の手順
- WAF の[カスタム応答](waf-front-door-configure-custom-response-code.md)を定義する方法を確認する
