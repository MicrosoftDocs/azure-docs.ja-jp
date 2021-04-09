---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91812706"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. 公式 Web サイトから OpenVPN クライアントをダウンロードしてインストールします。
1. ゲートウェイの VPN プロファイルをダウンロードします。 この操作は、Azure portal の [User VPN configurations]\(ユーザー VPN の構成\) タブまたは PowerShell の New-AzureRmVpnClientConfiguration で実行できます。
1. プロファイルを展開します。 メモ帳で OpenVPN フォルダーの vpnconfig.ovpn 構成ファイルを開きます。
1. P2S クライアント証明書セクションに、base64 の P2S クライアント証明書の公開キーを指定します。 PEM 形式の証明書の場合、.cer ファイルを開き、証明書ヘッダー間にある base64 キーを上書きしてコピーします。 手順については、[証明書をエクスポートしてエンコードされた公開キーを取得する方法](../articles/virtual-wan/certificates-point-to-site.md)に関するページを参照してください。
1. 秘密キー セクションに、base64 の P2S クライアント証明書の秘密キーを指定します。 手順については、[秘密キーを抽出する方法](../articles/virtual-wan/howto-openvpn-clients.md#windows)に関するセクションを参照してください。
1. その他のフィールドは変更しないでください。 クライアント入力に入力された構成を使用して VPN に接続します。
1. vpnconfig.ovpn ファイルを C:\Program Files\OpenVPN\config フォルダーにコピーします。
1. システム トレイの OpenVPN アイコンを右クリックし、 **[接続]** を選択します。

##### <a name="ikev2"></a>IKEv2

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。
1. パッケージをダブルクリックしてインストールします。 SmartScreen ポップアップが表示された場合は、 **[詳細]** 、 **[実行]** の順に選択します。
1. クライアント コンピューターで **[ネットワークの設定]** に移動し、 **[VPN]** を選択します。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。
1. 接続を試行する前に、クライアント コンピューターにクライアント証明書をインストール済みであることを確認します。 ネイティブ Azure 証明書の認証タイプを使用する場合、認証にはクライアント証明書が必要です。 証明書の生成の詳細については、「[証明書の生成](../articles/virtual-wan/certificates-point-to-site.md)」をご覧ください。 クライアント証明書のインストール方法については、[クライアント証明書のインストール](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページをご覧ください。
