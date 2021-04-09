---
title: Webhook サブスクライバー認証の構成 - Azure Event Grid IoT Edge | Microsoft Docs
description: Webhook サブスクライバー認証の構成
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d78b6e80005e9533ccd5ebfaea853f35ae6004be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171654"
---
# <a name="configure-webhook-subscriber-authentication"></a>Webhook サブスクライバー認証の構成

このガイドでは、Event Grid モジュールで可能な Webhook サブスクライバー構成の例を示します。 既定では、Webhook サブスクライバーに対して HTTPS エンドポイントのみが受け入れられます。 サブスクライバーが自己署名証明書を提示した場合は、Event Grid モジュールによって拒否されます。

## <a name="allow-only-https-subscriber"></a>HTTPS サブスクライバーのみを許可する

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>自己署名証明書を持つ HTTPS サブスクライバーを許可する

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>プロパティ `outbound__webhook__allowUnknownCA` は、一般的に自己署名証明書が使用される、テスト環境でのみ `true` に設定してください。 運用環境のワークロードでは、**false** に設定することをお勧めします。

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>証明書の検証をスキップして HTTPS サブスクライバーを許可する

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>プロパティ `outbound__webhook__skipServerCertValidation` は、認証される必要がある証明書を提示しないような、テスト環境でのみ `true` に設定してください。 運用環境のワークロードでは、**false** に設定することをお勧めします

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>自己署名証明書を使用して HTTP と HTTPS の両方を許可する

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>プロパティ `outbound__webhook__httpsOnly` は、HTTP サブスクライバーを先に起動することが望ましいような、テスト環境でのみ `false` に設定してください。 運用環境のワークロードでは、**true** に設定することをお勧めします
