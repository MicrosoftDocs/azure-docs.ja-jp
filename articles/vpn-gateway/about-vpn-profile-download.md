---
title: Azure VPN Gateway:P2S VPN クライアント プロファイルについて
description: これは、クライアント プロファイル ファイルを操作する際に役立ちます。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 5386cace7191be60534f0d2fbf4a85b592d1ecdd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151631"
---
# <a name="about-p2s-vpn-client-profiles"></a>P2S VPN クライアント プロファイルについて

ダウンロードしたプロファイル ファイルには、VPN 接続の構成に必要な情報が含まれています。 この記事は、VPN クライアント プロファイルに必要な情報を取得して理解するのに役立ちます。

## <a name="1-download-the-file"></a>1.ファイルをダウンロードする

次のコマンドを実行します。 プロファイルの ZIP ファイルをダウンロードするには、結果の URL をブラウザーにコピーします。

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2.ZIP ファイルを解凍する

zip ファイルを解凍します。 このファイルには、次のフォルダーが含まれています。

* AzureVPN
* ジェネリック
* OpenVPN (ゲートウェイで OpenVPN および Azure AD の認証設定を有効にした場合。 「[テナントの作成](openvpn-azure-ad-tenant.md)」を参照してください)。

## <a name="3-retrieve-information"></a>3.情報を取得する

**AzureVPN** フォルダーで、***azurevpnconfig.xml*** ファイルに移動し、メモ帳で開きます。 次のタグの間のテキストを書き留めておきます。

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>プロファイルの詳細

接続を追加するときは、前の手順で収集した情報をプロファイルの詳細ページで使用します。 各フィールドは次の情報に対応しています。

   * **Audience:** トークンの対象となる受信者リソースを識別します。
   * **Issuer:** トークンを生成したセキュリティ トークン サービス (STS) と Azure AD テナントを識別します。
   * **Tenant:** トークンを発行したディレクトリ テナントの変更できない一意の識別子が含まれます。
   * **FQDN:** Azure VPN Gateway での完全修飾ドメイン名 (FQDN)。
   * **ServerSecret:** VPN Gateway の事前共有キー。

## <a name="folder-contents"></a>フォルダーの内容

* **OpenVPN フォルダー**には、*ovpn* プロファイルが含まれています。このプロファイルは、キーと証明書を含めるために変更する必要があります。 詳細については、「[Azure VPN Gateway 用に OpenVPN クライアントを構成する](vpn-gateway-howto-openvpn-clients.md#windows)」をご覧ください。

* **generic フォルダー**には、パブリック サーバー証明書とVpnSettings.xml ファイルが含まれています。 VpnSettings.xml ファイルには、汎用クライアントの構成に必要な情報が含まれています。

* ダウンロードした ZIP ファイルには、**WindowsAmd64** および **WindowsX86** フォルダーも含まれている場合があります。 これらのフォルダーには、Windows クライアント用の SSTP および IKEv2 のインストーラーが含まれています。 それらをインストールするには、クライアントの管理者権限が必要です。

## <a name="next-steps"></a>次の手順

ポイント対サイトの詳細については、[ポイント対サイト](point-to-site-about.md)に関する記事をご覧ください。
