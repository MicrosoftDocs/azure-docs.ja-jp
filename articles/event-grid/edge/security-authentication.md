---
title: セキュリティと認証 - Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge での Event Grid のセキュリティと認証。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 42f6107ff79d6262cdc0a35cf972cf65d3a9a802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171382"
---
# <a name="security-and-authentication"></a>セキュリティと認証

セキュリティと認証は高度な概念であり、まず Event Grid の基礎を理解しておく必要があります。 IoT Edge 上で Event Grid を初めて使用する場合は、[こちら](concepts.md)から始めてください。 Event Grid モジュールは、IoT Edge の既存のセキュリティ インフラストラクチャ上に構築されます。 詳細と設定については、[このドキュメント](../../iot-edge/security.md)を参照してください。

以下のセクションでは、これらの設定がどのようにセキュリティで保護され、認証されるかについて詳しく説明します。

* TLS の構成
* 受信クライアント認証
* 発信サーバー認証
* 発信クライアント認証

>[!IMPORTANT]
>Event Grid モジュールのセキュリティと認証には、IoT Edge 上で使用できる既存のインフラストラクチャが利用されます。 IoT Edge サブシステムがセキュリティで保護されていることを前提としています。

>[!IMPORTANT]
>Event Grid の構成は、**既定でセキュリティで保護されています**。 以下のサブセクションでは、認証の側面をオーバーライドするために使用できるすべてのオプションと使用できる値について説明します。 影響を理解してから変更を加えてください。 変更を反映するには、Event Grid モジュールを再デプロイする必要があります。

## <a name="tls-configuration-aka-server-authentication"></a>TLS 構成 (別名はサーバー認証)

Event Grid モジュールでは、HTTP エンドポイントと HTTPS エンドポイントの両方がホストされます。 すべての IoT Edge モジュールには、IoT Edge のセキュリティ デーモンによってサーバー証明書が割り当てられます。 エンドポイントのセキュリティ保護には、サーバー証明書を使用します。 有効期限が切れると、IoT Edge セキュリティ デーモンの新しい証明書を使って、モジュールが自動的に更新されます。

既定では、HTTPS 通信のみが許可されています。 **inbound__serverAuth__tlsPolicy** 構成を介してこの動作をオーバーライドできます。 このプロパティに使用できる値を次の表にまとめています。

| 使用できる値 | 説明 |
| ---------------- | ------------ |
| 高 | 既定値。 HTTPS のみが有効になります
| Enabled | HTTP と HTTPS の両方が有効になります
| 無効 | HTTP のみが有効になります

## <a name="inbound-client-authentication"></a>受信クライアント認証

クライアントは、管理や実行時の操作を行うエンティティです。 クライアントは、他の IoT Edge モジュールや非 IoT アプリケーションの場合があります。

Event Grid モジュールでは、次の 2 種類のクライアント認証がサポートされています。

* Shared Access Signature (SAS) キーベース
* 証明書ベース

Event Grid モジュールは、既定では、証明書ベースの認証のみを受け入れるように構成されています。 起動時に、Event Grid モジュールによって IoT Edge セキュリティ デーモンから "TrustBundle" が取得され、それがクライアント証明書の検証に使用されます。 このチェーンに解決されないクライアント証明書は、`UnAuthorized` で拒否されます。

### <a name="certificate-based-client-authentication"></a>証明書ベースのクライアント認証

証明書ベースの認証は既定で有効です。 プロパティ **inbound__clientAuth__clientCert__enabled** を使用して、証明書ベースの認証を無効にすることもできます。 使用できる値を次の表にまとめます。

| 使用できる値 | 説明 |
| ----------------  | ------------ |
| true | 既定値。 クライアント証明書を提示するには、Event Grid モジュールへのすべての要求が必要です。 さらに、**inbound__clientAuth__clientCert__source** を構成する必要があります。
| false | クライアントには証明書の提示を強制しないでください。

**inbound__clientAuth__clientCert__source** に使用できる値を次の表にまとめます

| 使用できる値 | 説明 |
| ---------------- | ------------ |
| IoT Edge | 既定値。 IoT Edge の Trustbundle を使用して、すべてのクライアント証明書を検証します。

クライアントから自己署名を提示されると、既定で、Event Grid モジュールではそのような要求が拒否されます。 **inbound__clientAuth__clientCert__allowUnknownCA** プロパティを使用して、自己署名クライアント証明書の許可を選択できます。 使用できる値を次の表にまとめます。

| 使用できる値 | 説明 |
| ----------------  | ------------|
| true | 既定値。 自己署名証明書を正常に提示できるようにします。
| false | 自己署名証明書が提示された場合、要求は失敗します。

>[!IMPORTANT]
>運用環境のシナリオでは、**inbound__clientAuth__clientCert__allowUnknownCA** を **false** に設定することができます。

### <a name="sas-key-based-client-authentication"></a>SAS キーベースのクライアント認証

Event Grid モジュールでは、証明書ベースの認証に加え、SAS キーベースの認証を行うこともできます。 SAS キーは、すべての受信呼び出しの検証に使用する必要がある、Event Grid モジュールで構成されるシークレットに似ています。 クライアントは、HTTP ヘッダー "aeg-sas-key" にそのシークレットを指定する必要があります。 一致しない場合、要求は `UnAuthorized` で拒否されます。

SAS キーベースの認証を制御する構成は **inbound__clientAuth__sasKeys__enabled** です。

| 使用できる値 | 説明  |
| ----------------  | ------------ |
| true | SAS キーベースの認証を許可します。 **inbound__clientAuth__sasKeys__key1** または **inbound__clientAuth__sasKeys__key2** が必要です
| false | 既定値。 SAS キー ベースの認証は無効です。

 **inbound__clientAuth__sasKeys__key1** と **inbound__clientAuth__sasKeys__key2** は、受信要求に対して確認するように Event Grid モジュールを構成するキーです。 少なくとも 1 つのキーを構成する必要があります。 要求を行うクライアントは、要求ヘッダー "**aeg-sas-key**" の一部としてキーを提示する必要があります。 両方のキーが構成されている場合、クライアントはいずれかのキーを提示できます。

> [!NOTE]
>両方の認証方法を構成できます。 このような場合、SAS キーが最初にチェックされ、それが失敗した場合にのみ、証明書ベースの認証が実行されます。 要求を成功させるには、認証方法のいずれかのみを成功させる必要があります。

## <a name="outbound-client-authentication"></a>発信クライアント認証

発信コンテキストのクライアントからは Event Grid モジュールが参照されます。 実行中の操作からは、サブスクライバーにイベントが配信されます。 サブスクライブ モジュールはサーバーと見なされます。

すべての IoT Edge モジュールには、IoT Edge のセキュリティ デーモンによって ID 証明書が割り当てられます。 発信呼び出しには ID 証明書を使用します。 有効期限が切れると、IoT Edge セキュリティ デーモンの新しい証明書を使って、モジュールが自動的に更新されます。

発信クライアント認証を制御する構成は **outbound__clientAuth__clientCert__enabled** です。

| 使用できる値 | 説明 |
| ----------------  | ------------ |
| true | 既定値。 証明書提示するには、Event Grid モジュールからのすべての発信要が必要です。 **outbound__clientAuth__clientCert__source** を構成する必要があります。
| false | Event Grid モジュールで証明書を提示する必要はありません。

証明書のソースを制御する構成は **outbound__clientAuth__clientCert__source** です。

| 使用できる値 | 説明 |
| ---------------- | ------------ |
| IoT Edge | 既定値。 IoT Edge セキュリティ デーモンによって構成されたモジュールの ID 証明書を使用します。

### <a name="outbound-server-authentication"></a>発信サーバー認証

Event Grid サブスクライバーの送信先の種類の 1 つに "Webhook" があります。 既定では、このようなサブスクライバーに対して HTTPS エンドポイントのみが受け入れられます。

Webhook の送信先ポリシーを制御する構成は **outbound__webhook__httpsOnly** です。

| 使用できる値 | 説明 |
| ----------------  | ------------ |
| true | 既定値。 HTTPS エンドポイントがあるサブスクライバーのみを許可します。
| false | HTTP または HTTPS のいずれかのエンドポイントを使用するサブスクライバーを許可します。

Event Grid モジュールの既定では、サブスクライバーのサーバー証明書が検証されます。 検証をスキップするには、**outbound__webhook__skipServerCertValidation** をオーバーライドします。 次のいずれかの値になります。

| 使用できる値 | 説明 |
| ----------------  | ------------ |
| true | サブスクライバーのサーバー証明書を検証しません。
| false | 既定値。 サブスクライバーのサーバー証明書を検証します。

サブスクライバーの証明書が自己署名されている場合、既定で Event Grid モジュールではそのようなサブスクライバーが拒否されます。 自己署名証明書を許可するには、**outbound__webhook__allowUnknownCA** をオーバーライドします。 使用できる値を次の表にまとめます。

| 使用できる値 | 説明 |
| ----------------  | ------------ |
| true | 既定値。 自己署名証明書を正常に提示できるようにします。
| false | 自己署名証明書が提示された場合、要求は失敗します。

>[!IMPORTANT]
>運用環境のシナリオでは、**outbound__webhook__allowUnknownCA** を **false** に設定します。

> [!NOTE]
>IoT Edge 環境では、自己署名証明書が生成されます。 承認済みの CA から発行された証明書を運用環境のワークロードに対して生成し、受信と送信の両方で **allowUnknownCA** プロパティを **false** に設定することをお勧めします。

## <a name="summary"></a>まとめ

Event Grid モジュールは、**既定ではセキュリティで保護** されています。 運用環境のデプロイには、これらの既定値をそのまま使用することをお勧めします。

構成中に使用する基本原則を次に示します。

* モジュールへの HTTPS 要求のみを許可します。
* 証明書ベースのクライアント認証のみを許可します。 既知の CA から発行された証明書のみを許可します。 自己署名証明書を許可しません。
* SASKey ベースのクライアント認証を許可しません。
* 発信呼び出しに対しては常に Event Grid モジュールの ID 証明書を提示します。
* Webhook の送信先の種類の場合、HTTPS サブスクライバーのみを許可します。
* Webhook の送信先の種類の場合、必ずサブスクライバーのサーバー証明書を検証します。 既知の CA から発行された証明書のみを許可します。 自己署名証明書を許可しません。

既定で、Event Grid モジュールは次の構成でデプロイされます。

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
