---
title: Azure ポイント対サイト接続の問題のトラブルシューティング
titleSuffix: Azure VPN Gateway
description: ポイント対サイト接続に関する一般的な問題と、その他の仮想プライベート ネットワークのエラーおよび問題のトラブルシューティングと解決方法について学習します。
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: c316aaf02979008b9d2ebc691d54c0fb95a5a52d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994861"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>トラブルシューティング:Azure ポイント対サイト接続の問題

この記事では、発生する可能性があるポイント対サイト接続のよくある問題について説明します。 また、これらの問題の考えられる原因と解決策についても説明します。

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN クライアント エラー:証明書が見つかりませんでした

### <a name="symptom"></a>症状

VPN クライアントを使用して Azure 仮想ネットワークに接続しようとすると、次のエラー メッセージが表示されます。

**この拡張認証プロトコルで使用できる証明書が見つかりませんでした。(エラー 798)**

### <a name="cause"></a>原因

この問題は、 **Certificates - Current User\Personal\Certificates** にクライアント証明書がない場合に発生します。

### <a name="solution"></a>解決策

この問題を解決するには、次の手順に従ってください。

1. 証明書マネージャーを開く: **[開始]** をクリックし、「**コンピューター証明書の管理**」と入力して、検索結果の **[コンピューター証明書の管理]** をクリックします。

2. 次の証明書が正しい場所にあることを確認します。

    | Certificate | 場所 |
    | ------------- | ------------- |
    | AzureClient.pfx  | 現在のユーザー\個人\証明書 |
    | AzureRoot.cer    | ローカル コンピューター\信頼されたルート証明機関|

3. C:\Users\<UserName>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID> に移動し、ユーザーおよびコンピューターのストアに手動で証明書 (*.cer ファイル) をインストールします。

クライアント証明書をインストールする方法の詳細については、[ポイント対サイト接続の証明書の生成とエクスポート](vpn-gateway-certificates-point-to-site.md)に関する記事をご覧ください。

> [!NOTE]
> クライアント証明書をインポートする際は、 **[秘密キーの保護を強力にする]** オプションを選択しないでください。

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>リモート サーバーが応答していないため、お使いのコンピューターと VPN サーバーの間のネットワーク接続を確立できませんでした

### <a name="symptom"></a>症状

Windows で IKEv2 を使用し、Azure 仮想ネットワーク ゲートウェイに接続しようとすると、次のエラー メッセージが表示されます。

**リモート サーバーが応答していないため、お使いのコンピューターと VPN サーバーの間のネットワーク接続を確立できませんでした**

### <a name="cause"></a>原因
 
 Windows のバージョンで IKE 断片化がサポートされていない場合にこの問題が発生します
 
### <a name="solution"></a>解決策

IKEv2 は、Windows 10 および Windows Server 2016 でサポートされています。 ただし、IKEv2 を使用するには、更新プログラムをインストールして、ローカルでレジストリ キーの値を設定する必要があります。 Windows 10 以前の OS バージョンはサポートされておらず、それらのバージョンで使用できるのは SSTP のみになります。

Windows 10 または Windows Server 2016 を IKEv2 用に準備するには:

1. 更新プログラムをインストールします。

   | OS バージョン | Date | 数/リンク |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10 バージョン 1607 | 2018 年 1 月 17 日 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 バージョン 1703 | 2018 年 1 月 17 日 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 バージョン 1709 | 2018 年 3 月 22 日 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. レジストリ キーの値を設定します。 レジストリの `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` REG_DWORD キーを 1 として作成するか、1 に設定します。

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN クライアント エラー:予期していない、または形式が間違ったメッセージを受信しました

### <a name="symptom"></a>症状

VPN クライアントを使用して Azure 仮想ネットワークに接続しようとすると、次のエラー メッセージが表示されます。

**予期されない、または形式が間違ったメッセージを受信しました。(エラー 0x80090326)**

### <a name="cause"></a>原因

この問題は、次のいずれかの条件に当てはまる場合に発生します。

- ゲートウェイ サブネット上のユーザー定義ルート (UDR) と既定のルートが正しく設定されていない。
- ルート証明書の公開キーが Azure VPN ゲートウェイにアップロードされていない。 
- キーが破損しているか、有効期限が切れている。

### <a name="solution"></a>解決策

この問題を解決するには、次の手順に従ってください。

1. ゲートウェイ サブネット上の UDR を削除します。 UDR ですべてのトラフィックが正常に転送されるようにします。
2. Azure Portal でルート証明書の状態をチェックして、失効しているかどうかを確認します。 失効していない場合は、ルート証明書を削除して再アップロードしてみます。 詳細については、「[証明書の作成](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)」を参照してください。

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN クライアント エラー:証明書チェーンは処理されましたが、停止されました 

### <a name="symptom"></a>症状 

VPN クライアントを使用して Azure 仮想ネットワークに接続しようとすると、次のエラー メッセージが表示されます。

**証明書チェーンは処理されましたが、ルート証明書で停止されました。証明書は信頼プロバイダーから信頼されていません。**

### <a name="solution"></a>解決策

1. 次の証明書が正しい場所にあることを確認します。

    | Certificate | 場所 |
    | ------------- | ------------- |
    | AzureClient.pfx  | 現在のユーザー\個人\証明書 |
    | Azuregateway-*GUID*.cloudapp.net  | 現在のユーザー\信頼されたルート証明機関|
    | AzureGateway-*GUID*.cloudapp.net、AzureRoot.cer    | ローカル コンピューター\信頼されたルート証明機関|

2. 証明書が既にその場所にある場合は、証明書を削除してから再インストールしてみます。 **azuregateway-*GUID*.cloudapp.net** 証明書は、Azure Portal からダウンロードした VPN クライアント構成パッケージに含まれています。 ファイル アーカイブ処理を使用して、パッケージからファイルを抽出できます。

## <a name="file-download-error-target-uri-is-not-specified"></a>ファイル ダウンロード エラー:ターゲット URI が指定されていません

### <a name="symptom"></a>症状

次のエラー メッセージが表示されます。

**ファイル ダウンロード エラー。ターゲット URI が指定されていません。**

### <a name="cause"></a>原因 

この問題は、ゲートウェイの種類が正しくないことが原因で発生します。 

### <a name="solution"></a>解決策

VPN ゲートウェイの種類は **VPN** で、VPN の種類は **RouteBased** でなければなりません。

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN クライアント エラー:Azure VPN カスタム スクリプトの実行に失敗しました 

### <a name="symptom"></a>症状

VPN クライアントを使用して Azure 仮想ネットワークに接続しようとすると、次のエラー メッセージが表示されます。

**カスタム スクリプト (ルーティング テーブルを更新するため) の実行に失敗しました。(Error 8007026f)**

### <a name="cause"></a>原因

この問題は、ショートカットを使用してサイト対ポイント VPN 接続を開こうとした場合に発生することがあります。

### <a name="solution"></a>解決策 

VPN パッケージを、ショートカットからではなく、直接開きます。

## <a name="cannot-install-the-vpn-client"></a>VPN クライアントをインストールできない

### <a name="cause"></a>原因 

仮想ネットワークの VPN ゲートウェイを信頼するには、追加の証明書が必要です。 証明書は、Azure Portal から生成された VPN クライアント構成パッケージに含まれています。

### <a name="solution"></a>解決策

VPN クライアント構成パッケージを抽出し、.cer ファイルを検索します。 証明書をインストールするには、次の手順を実行します。

1. mmc.exe を開きます。
2. **証明書** スナップインを追加します。
3. ローカル コンピューターの **コンピューター** アカウントを選択します。
4. **[信頼されたルート証明機関]** ノードを右クリックします。 **[すべてのタスク]**  >  **[インポート]** の順にクリックし、VPN クライアント構成パッケージから抽出した .cer ファイルを参照します。
5. コンピューターを再起動します。 
6. VPN クライアントをインストールしてみます。

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure portal エラー:VPN ゲートウェイを保存できませんでした。データが無効です

### <a name="symptom"></a>症状

Azure Portal で VPN ゲートウェイの変更を保存しようとすると、次のエラー メッセージが表示されます。

**仮想ネットワーク ゲートウェイ &lt;*ゲートウェイ名*&gt; を保存できませんでした。証明書 &lt;*証明書 ID*&gt; のデータが無効です。**

### <a name="cause"></a>原因 

この問題は、アップロードしたルート証明書の公開キーにスペースなどの無効な文字が含まれている場合に発生する可能性があります。

### <a name="solution"></a>解決策

証明書のデータに改行 (キャリッジ リターン) などの無効な文字が含まれていないことを確認します。 値全体が 1 つの長い行である必要があります。 次のテキストは、証明書のサンプルです。

```text
-----BEGIN CERTIFICATE-----
MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
/0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
-----END CERTIFICATE-----
```

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure portal エラー:VPN ゲートウェイを保存できませんでした。リソース名は無効です

### <a name="symptom"></a>症状

Azure Portal で VPN ゲートウェイの変更を保存しようとすると、次のエラー メッセージが表示されます。 

**仮想ネットワーク ゲートウェイ &lt;*ゲートウェイ名*&gt; を保存できませんでした。リソース名 &lt;*アップロードしようとした証明書の名前*&gt; は無効です。**

### <a name="cause"></a>原因

この問題は、証明書の名前にスペースなどの無効な文字が含まれていることが原因で発生します。 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure portal エラー:VPN パッケージ ファイルのダウンロード エラー 503

### <a name="symptom"></a>症状

VPN クライアント構成パッケージをダウンロードしようとすると、次のエラー メッセージが表示されます。

**ファイルをダウンロードできませんでした。エラーの詳細: エラー 503。サーバーがビジーです。**
 
### <a name="solution"></a>解決策

このエラーは、一時的なネットワークの問題が原因で発生します。 数分経ってからもう一度 VPN パッケージをダウンロードしてみてください。

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Azure VPN Gateway のアップグレード:すべてのポイント対サイト クライアントが接続できません

### <a name="cause"></a>原因

証明書は、有効期間の半分を経過すると、ロールオーバーされます。

### <a name="solution"></a>解決策

この問題を解決するには、すべてのクライアントでポイント対サイト パッケージを再ダウンロードして再デプロイします。

## <a name="too-many-vpn-clients-connected-at-once"></a>一度に接続する VPN クライアントが多すぎる

接続の最大許容数に達しました。 接続されているクライアントの総数は、Azure Portal で確認できます。

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN クライアントがネットワーク ファイル共有にアクセスできない

### <a name="symptom"></a>症状

VPN クライアントは Azure 仮想ネットワークに接続しています。 しかし、クライアントはネットワーク共有にアクセスできません。

### <a name="cause"></a>原因

ファイル共有アクセスには、SMB プロトコルが使用されます。 接続の開始時にセッション資格情報が VPN クライアントによって追加されると、このエラーが発生します。 接続が確立されると、クライアントは Kerberos 認証にキャッシュ資格情報を使用するよう強制されます。 このプロセスにより、キー配布センター (ドメイン コントローラー) に対するクエリが開始され、トークンが取得されます。 クライアントはインターネットから接続するため、ドメイン コントローラーにアクセスできない場合があります。 そのため、クライアントは Kerberos から NTLM にフェールオーバーできません。 

クライアントが資格情報を求められるのは、クライアントが参加しているドメインによって発行された有効な証明書 (SAN=UPN) がクライアントにある場合のみです。 また、クライアントはドメイン ネットワークに物理的に接続されている必要があります。 この場合、クライアントはその証明書を使用して、ドメイン コントローラーにアクセスしようとします。 そうすると、キー配布センターは "KDC_ERR_C_PRINCIPAL_UNKNOWN" エラーを返します。 クライアントは強制的に NTLM にフェールオーバーされます。 

### <a name="solution"></a>解決策

この問題を回避するには、次のレジストリ サブキーからドメイン資格情報のキャッシュを無効にします。 

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1`


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>VPN クライアントを再インストールした後に Windows でポイント対サイト VPN 接続が見つからない

### <a name="symptom"></a>症状

ポイント対サイト VPN 接続を削除した後に、VPN クライアントを再インストールすると、 VPN 接続が正常に構成されません。 Windows の **[ネットワーク接続]** 設定に VPN 接続が表示されません。

### <a name="solution"></a>解決策

この問題を解決するには、**C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** から古い VPN クライアント構成ファイルを削除し、VPN クライアント インストーラーを再度実行します。

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>ポイント対サイト VPN クライアントがローカル ドメイン内のリソースの FQDN を解決できません

### <a name="symptom"></a>症状

クライアントがポイント対サイト VPN 接続を使用して Azure に接続するとき、ローカル ドメイン内のリソースの FQDN を解決できません。

### <a name="cause"></a>原因

ポイント対サイト VPN クライアントでは通常、Azure 仮想ネットワーク内に構成されている Azure DNS サーバーが使用されます。 (Ethernet インターフェイスのメトリックがより低い場合を除いて、) Azure DNS サーバーはクライアント内に構成されているローカル DNS サーバーに優先するため、すべての DNS クエリは Azure DNS サーバーに送信されます。 Azure DNS サーバーにローカル リソースのレコードがない場合、クエリは失敗します。

### <a name="solution"></a>解決策

問題を解決するには、Azure 仮想ネットワークで使用する Azure DNS サーバーがローカル リソースの DNS レコードを解決できることを確認します。 これを行うには、DNS フォワーダーまたは条件付きフォワーダーを使用できます。 詳細については、「[独自 DNS サーバー使用の名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>ポイント対サイト VPN 接続は確立されるが Azure リソースに接続できません 

### <a name="cause"></a>原因

この問題は、VPN クライアントが Azure VPN ゲートウェイからのルートを取得していない場合に発生する可能性があります。

### <a name="solution"></a>解決策

この問題を解決するには、[Azure VPN ゲートウェイをリセット](./reset-gateway.md)します。 新しいルートが使用されていることを確認するには、仮想ネットワーク ピアリングが正常に構成された後で、もう一度ポイント対サイト VPN クライアントをダウンロードする必要があります。

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>エラー:"失効サーバーがオフラインであったため、失効関数が失効を確認できませんでした。(エラー 0x80092013)"

### <a name="causes"></a>原因
このエラー メッセージは、クライアントが http://crl3.digicert.com/ssca-sha2-g1.crl と http://crl4.digicert.com/ssca-sha2-g1.crl にアクセスできない場合に発生します。  失効を確認するには、この 2 つのサイトにアクセスする必要があります。  この問題は、通常は、プロキシ サーバーが構成されているクライアントで発生します。 一部の環境では、要求がプロキシ サーバーを経由しない場合はエッジ ファイアウォールで拒否されます。

### <a name="solution"></a>解決策

プロキシ サーバーの設定を調べて、クライアントが http://crl3.digicert.com/ssca-sha2-g1.crl と http://crl4.digicert.com/ssca-sha2-g1.crl にアクセスできることを確認します。

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN クライアント エラー:RAS/VPN サーバーに構成されたポリシーにより、接続できませんでした。 (エラー 812)

### <a name="cause"></a>原因

このエラーは、VPN クライアントを認証するために使用する RADIUS サーバーの設定が正しくない場合、または Azure ゲートウェイが RADIUS サーバーに到達できない場合に発生します。

### <a name="solution"></a>解決策

RADIUS サーバーが正しく構成されていることを確認します。 詳細については、「[RADIUS 認証と Azure AD Multi-Factor Authentication Server の統合](../active-directory/authentication/howto-mfaserver-dir-radius.md)」を参照してください。

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>VPN Gateway からルート証明書をダウンロードするときに "エラー 405" が発生します

### <a name="cause"></a>原因

ルート証明書がインストールされていません。 ルート証明書は、クライアントの **信頼された証明書** ストアにインストールされます。

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN クライアント エラー:VPN トンネルの試行に失敗したため、リモート接続を確立できませんでした。 (エラー 800) 

### <a name="cause"></a>原因

NIC ドライバーの期限が切れています。

### <a name="solution"></a>解決策

NIC ドライバーを更新します。

1. **[開始]** をクリックして「**デバイス マネージャー**」と入力し、結果の一覧から選択します。 管理者パスワードまたは確認を求められたら、パスワードを入力するか、確認を行います。
2. **ネットワーク アダプター** カテゴリで、更新したい NIC を見つけます。  
3. デバイス名をダブルクリックして **[ドライバーの更新]** を選択し、 **[自動的に更新されたドライバ ソフトウェアを検索します]** を選択します。
4. Windows によって新しいドライバーが検出されない場合、デバイス製造元の Web サイト上にあるドライバーを探してその手順に従うことができます。
5. コンピューターを再起動して、もう一度接続を試してみてください。

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>VPN クライアント エラー:Dialing VPN connection <VPN Connection Name>, Status = VPN Platform did not trigger connection (VPN 接続 <VPN Connection Name> にダイヤルしています、状況 = VPN プラットフォームでは接続がトリガーされませんでした)

また、RasClient からイベント ビューアーに次のエラーが表示されることがあります: "ユーザー <User> は <VPN Connection Name> という接続にダイヤルし、失敗しました。 失敗時に返された理由コードは 1460 です。"

### <a name="cause"></a>原因

Windows のアプリ設定で、Azure VPN クライアントについては [バックグラウンド アプリ] アプリのアクセス許可が有効になっていません。

### <a name="solution"></a>解決策

1. Windows で、[設定]-> [プライバシー]-> [バックグラウンドアプリ] にアクセスします
2. [アプリのバックグラウンド実行を許可する] をオンに切り替えます

## <a name="error-file-download-error-target-uri-is-not-specified"></a>エラー:'ファイル ダウンロード エラー: ターゲット URI が指定されていません'

### <a name="cause"></a>原因

これは、ゲートウェイの種類が正しく構成されていないことが原因です。

### <a name="solution"></a>解決策

Azure VPN ゲートウェイの種類は VPN で、VPN の種類は **RouteBased** にする必要があります。

## <a name="vpn-package-installer-doesnt-complete"></a>VPN パッケージのインストーラーが完了しない

### <a name="cause"></a>原因

この問題の原因は、以前の VPN クライアントのインストールである可能性があります。 

### <a name="solution"></a>解決策

**C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** から古い VPN クライアント構成ファイルを削除し、VPN クライアント インストーラーを再度実行します。 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>VPN クライアントがしばらくしたら休止状態またはスリープ状態になる

### <a name="solution"></a>解決策

VPN クライアントが実行されているコンピューターのスリープ設定および休止状態設定をチェックします。