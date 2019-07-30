---
title: Azure VPN Gateway 用に OpenVPN クライアントを構成する方法 | Microsoft Docs
description: Azure VPN Gateway 用に OpenVPN クライアントを構成する手順
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: cherylmc
ms.openlocfilehash: b8f1626da730178d2cd9c2f31c4f9876102b3d46
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477841"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Azure VPN Gateway 用に OpenVPN クライアントを構成する

この記事では、**OpenVPN ® プロトコル** クライアントの構成方法について説明します。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

VPN ゲートウェイ用に OpenVPN を構成する手順を完了していることを確認します。 詳細については、[Azure VPN Gateway 用に OpenVPN を構成する方法](vpn-gateway-howto-openvpn.md)に関するページを参照してください。

## <a name="windows"></a>Windows クライアント

1. 公式の [OpenVPN Web サイト](https://openvpn.net/index.php/open-source/downloads.html)から OpenVPN クライアント (バージョン 2.4 以降) をダウンロードしてインストールします。
2. ゲートウェイの VPN プロファイルをダウンロードします。 この操作は、Azure portal の [ポイント対サイト構成] タブまたは PowerShell の "New-AzVpnClientConfiguration" で実行できます。
3. プロファイルを展開します。 次に、メモ帳を使って OpenVPN フォルダーの *vpnconfig.ovpn* 構成ファイルを開きます。
4. 作成してゲートウェイ上の P2S 構成にアップロードした P2S クライアント証明書を[エクスポート](vpn-gateway-certificates-point-to-site.md#clientexport)します。
5. *.pfx* から秘密キーと base64 のサムプリントを抽出します。 これには複数の方法があります。 1 つの方法として、コンピューター上で OpenSSL を使用します。 *profileinfo.txt* ファイルには、CA およびクライアント証明書の秘密キーとサムプリントが含まれています。 必ず、クライアント証明書のサムプリントを使用してください。

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. メモ帳で *profileinfo.txt* を開きます。 クライアント (子) 証明書のサムプリントを取得するには、子の証明書のテキスト "-----BEGIN CERTIFICATE-----" および "-----END CERTIFICATE-----" (これらとその間のテキストを含む) を選択してコピーします。 subject=/ 行を確認して、子の証明書を識別できます。
7. 手順 3 からメモ帳で開いている *vpnconfig.ovpn* ファイルに切り替えます。 以下に示したセクションを検索して、"cert" と "/cert" の間をすべて置き換えます。

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. メモ帳で *profileinfo.txt* を開きます。 秘密キーを取得するには、"-----BEGIN PRIVATE KEY-----" および "-----END PRIVATE KEY-----" (とその間の) テキストを選択してコピーします。
9. メモ帳の vpnconfig.ovpn ファイルに戻って、このセクションを見つけます。 秘密キーを貼り付けて、"key" と "/key" の間をすべて置き換えます。

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. その他のフィールドは変更しないでください。 クライアント入力に入力された構成を使用して VPN に接続します。
11. vpnconfig.ovpn ファイルを C:\Program Files\OpenVPN\config フォルダーにコピーします。
12. システム トレイの OpenVPN アイコンを右クリックし、[接続] をクリックします。

## <a name="mac"></a>Mac クライアント

1. [TunnelBlick](https://tunnelblick.net/downloads.html) などの OpenVPN クライアントをダウンロードしてインストールします。 
2. ゲートウェイの VPN プロファイルをダウンロードします。 この操作は、Azure portal の [ポイント対サイト構成] タブから、または PowerShell の "New-AzVpnClientConfiguration" を使用して、実行できます。
3. プロファイルを展開します。 メモ帳で OpenVPN フォルダーから vpnconfig.ovpn 構成ファイルを開きます。
4. P2S クライアント証明書セクションに、base64 の P2S クライアント証明書の公開キーを指定します。 PEM 形式の証明書の場合、.cer ファイルを開き、証明書ヘッダー間にある base64 キーを上書きしてコピーします。 エンコードされた公開キーを取得する証明書をエクスポートする方法については、[公開キーのエクスポート](vpn-gateway-certificates-point-to-site.md#cer)に関するページを参照してください。
5. 秘密キー セクションに、base64 の P2S クライアント証明書の秘密キーを指定します。 秘密キーの抽出方法については、[秘密キーのエクスポート](https://openvpn.net/community-resources/how-to/#pki)に関するページを参照してください。
6. その他のフィールドは変更しないでください。 クライアント入力に入力された構成を使用して VPN に接続します。
7. プロファイル ファイルをダブルクリックして、Tunnelblick にプロファイルを作成します。
8. アプリケーション フォルダーから Tunnelblick を起動します。
9. システム トレイの Tunnelblick アイコンをクリックし、[接続] を選択します。

> [!IMPORTANT]
>OpenVPN プロトコルでは、iOS 11.0 以降と MacOS 10.13 以降のみがサポートされます。
>

## <a name="linux"></a>Linux クライアント

1. 新しいターミナル セッションを開きます。 新しいセッションを開くには、Ctrl キーと Alt キーを押しながら t キーを押します。
2. 次のコマンドを入力して、必要なコンポーネントをインストールします。

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. ゲートウェイの VPN プロファイルをダウンロードします。 この操作は、Azure portal の [ポイント対サイト構成] タブで実行できます。
4. 作成してゲートウェイ上の P2S 構成にアップロードした P2S クライアント証明書を[エクスポート](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport)します。 
5. .pfx から秘密キーと base64 サムプリントを抽出します。 これには複数の方法があります。 1 つの方法として、コンピューター上で OpenSSL を使用します。

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   *profileinfo.txt* ファイルには、CA およびクライアント証明書の秘密キーとサムプリントが含まれています。 必ず、クライアント証明書のサムプリントを使用してください。

6. テキスト エディターで *profileinfo.txt* を開きます。 クライアント (子) 証明書のさむプリントを取得するには、子の証明書の "-----BEGIN CERTIFICATE-----" および "-----END CERTIFICATE-----" とその間のテキストを選択してコピーします。 subject=/ 行を確認して、子の証明書を識別できます。

7. *vpnconfig.ovpn* ファイルを開いて、以下に示したセクションを見つけます。 "cert" と "/cert" の間をすべて置き換えます。

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. テキスト エディターで profileinfo.txt を開きます。 秘密キーを取得するには、"-----BEGIN PRIVATE KEY-----" および "-----END PRIVATE KEY-----" とその間のテキストを選択してコピーします。

9. テキスト エディターで vpnconfig.ovpn ファイルを開き、このセクションを見つけます。 秘密キーを貼り付けて、"key" と "/key" の間をすべて置き換えます。

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. その他のフィールドは変更しないでください。 クライアント入力に入力された構成を使用して VPN に接続します。
11. コマンド ラインを使用して接続するには、以下のコマンドを入力します。
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>
    ```
12. GUI を使用して接続するには、システム設定に移動します。
13. **+** をクリックして新しい VPN 接続を追加します。
14. **[VPN の追加]** で **[ファイルからインポート]** を選択します。
15. プロファイル ファイルを参照して、ダブルクリックするか **[開く]** を選択します。
16. **[VPN の追加]** ウィンドウで **[追加]** をクリックします。
  
    ![ファイルからインポート](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. 接続するには、 **[ネットワーク設定]** ページで VPN を**オン**にするか、システム トレイのネットワーク アイコンを選択します。

## <a name="next-steps"></a>次の手順

VPN クライアントが別の VNet のリソースにアクセスできるようにするには、[VNet 間](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関する記事に記載されている手順に従って、VNet 間接続を設定します。 ゲートウェイと接続で BGP を有効にします。そうしないと、トラフィックは流れません。

**"OpenVPN" は OpenVPN Inc. の商標です。**
