---
title: "Azure ポイント対サイト接続の問題のトラブルシューティング | Microsoft Docs"
description: "ポイント対サイト接続の問題のトラブルシューティング方法について説明します。"
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: de37c8ffd47a2b8e201d18e3a20b5325d528ad59
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>トラブルシューティング: Azure ポイント対サイト接続の問題

この記事では、発生する可能性があるポイント対サイト接続のよくある問題について説明します。 また、これらの問題の考えられる原因と解決策についても説明します。

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN クライアント エラー: 証明書が見つかりませんでした

### <a name="symptom"></a>症状

VPN クライアントを使用して Azure 仮想ネットワークに接続しようとすると、次のエラー メッセージが表示されます。

**この拡張認証プロトコルで使用できる証明書が見つかりませんでした。(エラー 798)**

### <a name="cause"></a>原因

この問題は、 **Certificates - Current User\Personal\Certificates** にクライアント証明書がない場合に発生します。

### <a name="solution"></a>解決策

クライアント証明書が証明書ストア (Certmgr.msc) の次の場所にインストールされていることを確認します。
 
**証明書 - 現在のユーザー\個人\証明書**

クライアント証明書をインストールする方法の詳細については、[ポイント対サイト接続の証明書の生成とエクスポート](vpn-gateway-certificates-point-to-site.md)に関する記事をご覧ください。

> [!NOTE]
> クライアント証明書をインポートする際は、**[秘密キーの保護を強力にする]** オプションを選択しないでください。

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN クライアント エラー: 予期されない、または形式が間違ったメッセージを受信しました

### <a name="symptom"></a>症状

VPN クライアントを使用して Azure 仮想ネットワークに接続しようとすると、次のエラー メッセージが表示されます。

**予期されない、または形式が間違ったメッセージを受信しました。(エラー 0x80090326)**

### <a name="cause"></a>原因

この問題は、ルート証明書の公開キーが Azure VPN ゲートウェイにアップロードされていない場合に発生します。 また、キーが破損しているか、有効期限が切れている場合にも発生することがあります。

### <a name="solution"></a>解決策

この問題を解決するには、Azure Portal でルート証明書の状態をチェックして、失効しているかどうかを確認します。 失効していない場合は、ルート証明書を削除して再アップロードしてみます。 詳細については、「[証明書の作成](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)」を参照してください。

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN クライアント エラー: 証明書チェーンは処理されましたが、停止されました 

### <a name="symptom"></a>症状 

VPN クライアントを使用して Azure 仮想ネットワークに接続しようとすると、次のエラー メッセージが表示されます。

**証明書チェーンは処理されましたが、ルート証明書で停止されました。証明書は信頼プロバイダーから信頼されていません。**

### <a name="solution"></a>解決策

1. 次の証明書が正しい場所にあることを確認します。

    | 証明書 | 場所 |
    | ------------- | ------------- |
    | AzureClient.pfx  | 現在のユーザー\個人\証明書 |
    | Azuregateway-*GUID*.cloudapp.net  | 現在のユーザー\信頼されたルート証明機関|
    | AzureGateway-*GUID*.cloudapp.net、AzureRoot.cer    | ローカル コンピューター\信頼されたルート証明機関|

2. 証明書が既にその場所にある場合は、証明書を削除してから再インストールしてみます。 **azuregateway-*GUID*.cloudapp.net** 証明書は、Azure Portal からダウンロードした VPN クライアント構成パッケージに含まれています。 ファイル アーカイブ処理を使用して、パッケージからファイルを抽出できます。

## <a name="file-download-error-target-uri-is-not-specified"></a>ファイル ダウンロード エラー: ターゲット URI が指定されていません

### <a name="symptom"></a>症状

次のエラー メッセージが表示されます。

**ファイル ダウンロード エラー: ターゲット URI が指定されていません。**

### <a name="cause"></a>原因 

この問題は、ゲートウェイの種類が正しくないことが原因で発生します。 

### <a name="solution"></a>解決策

VPN ゲートウェイの種類は **VPN** で、VPN の種類は **RouteBased** でなければなりません。

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN クライアント エラー: Azure VPN カスタム スクリプトの実行に失敗しました 

### <a name="symptom"></a>症状

VPN クライアントを使用して Azure 仮想ネットワークに接続しようとすると、次のエラー メッセージが表示されます。

**カスタム スクリプト (ルーティング テーブルを更新するため) の実行に失敗しました。(エラー 8007026f)**

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
2. **証明書**スナップインを追加します。
3. ローカル コンピューターの**コンピューター** アカウントを選択します。
4. **[信頼されたルート証明機関]** ノードを右クリックします。 **[すべてのタスク]** > **[インポート]** の順にクリックし、VPN クライアント構成パッケージから抽出した .cer ファイルを参照します。
5. コンピューターを再起動します。 
6. VPN クライアントをインストールしてみます。

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure Portal エラー: VPN ゲートウェイを保存できませんでした。データが無効です

### <a name="symptom"></a>症状

Azure Portal で VPN ゲートウェイの変更を保存しようとすると、次のエラー メッセージが表示されます。

**仮想ネットワーク ゲートウェイ &lt;*ゲートウェイ名*&gt; を保存できませんでした。 証明書 &lt;*証明書 ID*&gt; のデータが無効です。**

### <a name="cause"></a>原因 

この問題は、アップロードしたルート証明書の公開キーにスペースなどの無効な文字が含まれている場合に発生する可能性があります。

### <a name="solution"></a>解決策

証明書のデータに改行 (キャリッジ リターン) などの無効な文字が含まれていないことを確認します。 値全体が 1 つの長い行である必要があります。 次のテキストは、証明書のサンプルです。

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure Portal エラー: VPN ゲートウェイを保存できませんでした。リソース名は無効です

### <a name="symptom"></a>症状

Azure Portal で VPN ゲートウェイの変更を保存しようとすると、次のエラー メッセージが表示されます。 

**仮想ネットワーク ゲートウェイ &lt;*ゲートウェイ名*&gt; を保存できませんでした。 リソース名 &lt;*アップロードしようとした証明書の名前*&gt; は無効です**。

### <a name="cause"></a>原因

この問題は、証明書の名前にスペースなどの無効な文字が含まれていることが原因で発生します。 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure Portal エラー: VPN パッケージ ファイルのダウンロード エラー 503

### <a name="symptom"></a>症状

VPN クライアント構成パッケージをダウンロードしようとすると、次のエラー メッセージが表示されます。

**ファイルをダウンロードできませんでした。エラーの詳細: エラー 503。サーバーがビジーです。**
 
### <a name="solution"></a>解決策

このエラーは、一時的なネットワークの問題が原因で発生します。 数分経ってからもう一度 VPN パッケージをダウンロードしてみてください。

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>Azure VPN Gateway のアップグレード: すべての P2S クライアントが接続できません

### <a name="cause"></a>原因

証明書は、有効期間の半分を経過すると、ロールオーバーされます。

### <a name="solution"></a>解決策

この問題を解決するには、新しい証明書を作成して VPN クライアントに再配布します。 

## <a name="too-many-vpn-clients-connected-at-once"></a>一度に接続する VPN クライアントが多すぎる

VPN ゲートウェイあたりの接続の最大許容数は 128 です。 接続されているクライアントの総数は、Azure Portal で確認できます。

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>ポイント対サイト VPN でルート テーブルに 10.0.0.0/8 のルートが誤って追加される

### <a name="symptom"></a>症状

ポイント対サイト クライアント上で VPN 接続をダイヤルすると、VPN クライアントにより Azure 仮想ネットワークにルートが追加されます。 IP ヘルパー サービスによって、VPN クライアントのサブネットのルートが追加されます。 

VPN クライアントの範囲が 10.0.0.0/8 の小規模なサブネット (10.0.12.0/24 など) に属する場合、 10.0.12.0/24 のルートではなく、優先順位の高い 10.0.0.0/8 のルートが追加されます。　 

この不適切なルートにより、具体的なルートが定義されていない 10.50.0.0/24 など、10.0.0.0/8 の範囲内の別のサブネットに属する他のオンプレミス ネットワークとの接続が切断されます。 

### <a name="cause"></a>原因

この動作は、Windows クライアントの仕様です。 クライアントは、PPP IPCP プロトコルを使用する場合、サーバー (この場合は VPN ゲートウェイ) からトンネル インターフェイスの IP アドレスを取得します。 ただし、このプロトコルの制限により、クライアントにはサブネット マスクがありません。 サブネット マスクを取得する方法が他にないため、クライアントはトンネル インターフェイスの IP アドレスのクラスに基づいてサブネット マスクを推測しようとします。 

そのため、次の静的マッピングに基づいてルートが追加されます。 

アドレスがクラス A に属している場合 --> /8 が適用されます

アドレスがクラス B に属している場合 --> /16 が適用されます

アドレスがクラス C に属している場合 --> /24 が適用されます

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN クライアントがネットワーク ファイル共有にアクセスできない

### <a name="symptom"></a>症状

VPN クライアントは Azure 仮想ネットワークに接続しています。 しかし、クライアントはネットワーク共有にアクセスできません。

### <a name="cause"></a>原因

ファイル共有アクセスには、SMB プロトコルが使用されます。 接続の開始時にセッション資格情報が VPN クライアントによって追加されると、このエラーが発生します。 接続が確立されると、クライアントは Kerberos 認証にキャッシュ資格情報を使用するよう強制されます。 このプロセスにより、キー配布センター (ドメイン コントローラー) に対するクエリが開始され、トークンが取得されます。 クライアントはインターネットから接続するため、ドメイン コントローラーにアクセスできない場合があります。 そのため、クライアントは Kerberos から NTLM にフェールオーバーできません。 

クライアントが資格情報を求められるのは、クライアントが参加しているドメインによって発行された有効な証明書 (SAN=UPN) がクライアントにある場合のみです。 また、クライアントはドメイン ネットワークに物理的に接続されている必要があります。 この場合、クライアントはその証明書を使用して、ドメイン コントローラーにアクセスしようとします。 そうすると、キー配布センターは "KDC_ERR_C_PRINCIPAL_UNKNOWN" エラーを返します。 クライアントは強制的に NTLM にフェールオーバーされます。 

### <a name="solution"></a>解決策

この問題を回避するには、次のレジストリ サブキーからドメイン資格情報のキャッシュを無効にします。 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>VPN クライアントを再インストールした後に Windows でポイント対サイト VPN 接続が見つからない

### <a name="symptom"></a>症状

ポイント対サイト VPN 接続を削除した後に、VPN クライアントを再インストールすると、 VPN 接続が正常に構成されません。 Windows の **[ネットワーク接続]** 設定に VPN 接続が表示されません。

### <a name="solution"></a>解決策

この問題を解決するには、**C:\Users\TheUserName\AppData\Roaming\Microsoft\Network\Connections** から古い VPN クライアント構成ファイルを削除し、VPN クライアント インストーラーを再度実行します。

