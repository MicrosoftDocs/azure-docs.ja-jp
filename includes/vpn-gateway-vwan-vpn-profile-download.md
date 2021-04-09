---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979073"
---
## <a name="extract-the-zip-file"></a>ZIP ファイルを解凍する

zip ファイルを解凍します。 このファイルには、次のフォルダーが含まれています。

* AzureVPN
* ジェネリック
* OpenVPN (ゲートウェイで OpenVPN と **Azure 証明書** または **RADIUS 認証** の設定を有効にした場合)。 テナントを作成するための構成に対応する適切な記事を選択します。

  * [VPN Gateway - テナントを作成する](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)。
  * [Virtual WAN - テナントを作成する](../articles/virtual-wan/openvpn-azure-ad-tenant.md)。

## <a name="retrieve-information"></a>情報を取得する

**AzureVPN** フォルダーで、**_azurevpnconfig.xml_** ファイルに移動し、メモ帳で開きます。 次のタグの間のテキストを書き留めておきます。

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>プロファイルの詳細

接続を追加するときは、前の手順で収集した情報をプロファイルの詳細ページで使用します。 各フィールドは次の情報に対応しています。

* **Audience:** トークンの対象である受信者のリソースを識別します。
* **Issuer:** トークンを生成したセキュリティ トークン サービス (STS) と Azure AD テナントを識別します。
* **Tenant:** トークンを発行したディレクトリ テナントの変更できない一意の識別子が含まれます。
* **FQDN:** Azure VPN Gateway での完全修飾ドメイン名 (FQDN)。
* **ServerSecret:** VPN Gateway の事前共有キー。

## <a name="folder-contents"></a>フォルダーの内容

* **generic フォルダー** には、パブリック サーバー証明書とVpnSettings.xml ファイルが含まれています。 VpnSettings.xml ファイルには、汎用クライアントの構成に必要な情報が含まれています。

* ダウンロードした ZIP ファイルには、**WindowsAmd64** および **WindowsX86** フォルダーも含まれている場合があります。 これらのフォルダーには、Windows クライアント用の SSTP および IKEv2 のインストーラーが含まれています。 それらをインストールするには、クライアントの管理者権限が必要です。
