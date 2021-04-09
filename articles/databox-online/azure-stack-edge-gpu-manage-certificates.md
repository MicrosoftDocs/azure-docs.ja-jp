---
title: Azure Stack Edge Pro GPU で証明書を使用する | Microsoft Docs
description: 証明書を使用する理由、証明書の種類、デバイスへの証明書のアップロード方法など、Azure Stack Edge Pro GPU デバイスでの証明書の使用について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 534870e6bd67b7aa5273289f3154a794a2b9bd22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519012"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスで証明書を使用する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro デバイスにインストールできる証明書の種類について説明します。 この記事には、各証明書の種類の詳細と、証明書をインストールして、有効期限を識別する手順も記載します。  

## <a name="about-certificates"></a>証明書について

証明書は、**公開キー** と、信頼されたサード パーティ (**証明機関** など) が **署名** (検証) したエンティティ (ドメイン名など) を結び付けるものです。  証明書は、信頼された公開暗号化キーを配布するための便利な方法を提供します。 証明書によって、通信が信頼できるものであること、暗号化された情報を正しいサーバーに送信していることが確認されます。 

Azure Stack Edge Pro デバイスが最初に構成されたときに、自己署名証明書が自動的に生成されます。 必要に応じて、独自の証明書を導入できます。 独自の証明書を導入する場合は、従わなければならないガイドラインがあります。

## <a name="types-of-certificates"></a>証明書の種類

Azure Stack Edge Pro デバイスで使用される各種の証明書を次に示します。 
- 署名証明書
    - ルート CA
    - 中級

- エンドポイント証明書
    - ノード証明書
    - ローカル UI 証明書
    - Azure Resource Manager 証明書
    - BLOB ストレージ証明書
    - IoT デバイス証明書
    <!--- WiFi certificates
    - VPN certificates-->

- 暗号化証明書
    - サポート セッション証明書

これらの証明書それぞれについて、次のセクションで詳しく説明します。

## <a name="signing-chain-certificates"></a>署名チェーン証明書

証明書に署名する機関または署名証明機関の証明書です。 

### <a name="types"></a>種類

これに該当する証明書は、ルート証明書または中間証明書です。 ルート証明書は常に自己署名されています (つまり、それ自体によって署名されています)。 中間証明書は自己署名ではなく、署名機関によって署名されています。

### <a name="caveats"></a>注意事項

- ルート証明書は、署名チェーン証明書である必要があります。
- ルート証明書は、次の形式でデバイスにアップロードできます。 
    - **DER** – `.cer` ファイル拡張子として使用できます。
    - **Base-64 エンコード** – `.cer` ファイル拡張子として使用できます。
    - **P7b** – この形式は、ルート証明書と中間証明書を含む署名チェーン証明書に対してのみ使用されます。
- 署名チェーン証明書は常に、他の証明書をアップロードする前にアップロードされます。


## <a name="node-certificates"></a>ノード証明書

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> デバイス内のすべてのノードは常に相互に通信しているため、信頼関係が必要です。 ノード証明書は、その信頼を確立する手段を提供します。 https 経由のリモート PowerShell セッションを使用してデバイス ノードに接続するときにも、ノード証明書が関与します。

### <a name="caveats"></a>注意事項

- ノード証明書は、エクスポート可能な秘密キーを含む `.pfx` 形式で提供する必要があります。 
- 1 つのワイルドカード ノード証明書または 4 つの個別ノード証明書を作成してアップロードできます。 
- DNS ドメインが変更されたが、デバイス名は変わらない場合は、ノード証明書を変更する必要があります。 独自のノード証明書を導入する場合、デバイスのシリアル番号は変更できず、ドメイン名のみ変更できます。
- ノード証明書を作成する場合、次の表を参考にしてください。
   
    |Type |サブジェクト名 (SN)  |サブジェクトの別名 (SAN)  |サブジェクト名の例 |
    |---------|---------|---------|---------|
    |Node|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>エンドポイント証明書

デバイスが公開するエンドポイントでは、信頼された通信のために証明書が必要です。 エンドポイント証明書には、REST API を使用して Azure Resource Manager および BLOB ストレージにアクセスするときに必要なものが含まれています。 

独自の署名入り証明書を導入する場合は、証明書の対応する署名チェーンも必要です。 署名チェーン、Azure Resource Manager、デバイスの BLOB 証明書については、デバイスを認証して通信するために、クライアント マシンにも対応する証明書が必要になります。

### <a name="caveats"></a>注意事項

- エンドポイント証明書は、秘密キーを使用した `.pfx` 形式である必要があります。 署名チェーンは DER 形式 (`.cer` ファイル拡張子) である必要があります。 
- 独自のエンドポイント証明書を導入する場合、個々の証明書またはマルチドメイン証明書にすることができます。 
- 署名チェーンを導入する場合、エンドポイント証明書をアップロードする前に署名チェーン証明書をアップロードする必要があります。
- デバイス名または DNS ドメイン名が変更された場合、これらの証明書を変更する必要があります。
- ワイルドカード エンドポイント証明書を使用できます。
- エンドポイント証明書のプロパティは、一般的な SSL 証明書のプロパティに似ています。 
- エンドポイント証明書を作成するときは、次の表を使用します。

    |Type |サブジェクト名 (SN)  |サブジェクトの別名 (SAN)  |サブジェクト名の例 |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |BLOB ストレージ|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |両方のエンドポイントに対する単一のマルチ SAN 証明書|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>ローカル UI 証明書

ブラウザーを使用して、デバイスのローカル Web UI にアクセスできます。 この通信をセキュリティで確実に保護するために、独自の証明書をアップロードできます。 

### <a name="caveats"></a>注意事項

- ローカル UI 証明書も、エクスポート可能な秘密キーを含む `.pfx` 形式でアップロードします。
- ローカル UI 証明書をアップロードした後、ブラウザーを再起動してキャッシュをクリアする必要があります。 お使いのブラウザーの具体的な手順を参照してください。

    |Type |サブジェクト名 (SN)  |サブジェクトの別名 (SAN)  |サブジェクト名の例 |
    |---------|---------|---------|---------|
    |ローカル UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge デバイス証明書

Azure Stack Edge Pro デバイスも IoT デバイスの一種であり、IoT Edge デバイスが接続されていることにより、コンピューティングが有効になっています。 この IoT Edge デバイスと、それに接続可能なダウンストリーム デバイスの間でセキュリティで保護された通信を行うために、IoT Edge 証明書をアップロードすることもできます。 

このデバイスには、デバイスでコンピューティング シナリオのみ使用する場合に使用できる自己署名証明書が備わっています。 ただし、Azure Stack Edge Pro デバイスがダウンストリーム デバイスに接続されている場合は、独自の証明書を導入する必要があります。

この信頼関係を有効にするために、次の 3 つの IoT Edge 証明書をインストールする必要があります。

- **ルート証明機関または所有者証明機関**
- **デバイス証明機関** 
- **デバイス キー証明書**

### <a name="caveats"></a>注意事項

- IoT Edge 証明書は `.pem` 形式でアップロードします。 


IoT Edge 証明書の詳細については、[Azure IoT Edge 証明書の詳細](../iot-edge/iot-edge-certs.md#iot-edge-certificates)に関するページを参照してください。

## <a name="support-session-certificates"></a>サポート セッション証明書

Azure Stack Edge Pro デバイスで問題が発生した場合、その問題をトラブルシューティングするために、デバイスでリモート PowerShell サポート セッションを開くことができます。 セキュリティで保護された暗号化通信をこのサポート セッションで有効にするために、証明書をアップロードできます。

### <a name="caveats"></a>注意事項

- 暗号化解除ツールを使用して、対応する `.pfx` 証明書および秘密キーがクライアント コンピューターにインストールされていることを確認してください。
- 証明書の **[キー使用法]** フィールドが **[証明書の署名]** ではないことを確認します。 これを確認するには、証明書を右クリックし、 **[開く]** を選択し、 **[詳細]** タブで **[キー使用法]** を見つけます。 


### <a name="caveats"></a>注意事項

- サポート セッション証明書は、`.cer` 拡張子を持つ DER 形式で用意する必要があります。


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>証明書を作成する (省略可能)

次のセクションでは、署名チェーンとエンドポイント証明書を作成する手順について説明します。

### <a name="certificate-workflow"></a>証明書のワークフロー

環境内で動作するデバイスの証明書を作成するための定められた方法を持つことになります。 IT 管理者によって提供された証明書を使用できます。 

**開発またはテストの目的に限り、Windows PowerShell を使用して、ローカル システムで証明書を作成することもできます。** クライアントの証明書を作成するときは、次のガイドラインに従ってください。

1. 次の任意の種類の証明書を作成できます。

    - 単一の完全修飾ドメイン名 (FQDN) で使用できる単一の証明書を作成する。 たとえば、*mydomain.com* などです。
    - メイン ドメイン名と複数のサブドメインをセキュリティで保護するために、ワイルドカード証明書を作成する。 たとえば、* *.mydomain.com* などです。
    - 1 つの証明書で複数のドメイン名を対象とするサブジェクトの別名 (SAN) 証明書を作成する。 

2. 独自の証明書を導入する場合、署名チェーンのルート証明書が必要になります。 手順については、「[署名チェーン証明書を作成する](#create-signing-chain-certificate)」を参照してください。

3. 次に、アプライアンス、BLOB、Azure Resource Manager のエンドポイント証明書をローカル UI に対して作成できます。 アプライアンス、BLOB、Azure Resource Manager 用に 3 つの個別の証明書を作成することも、3 つのエンドポイントすべてに対して 1 つの証明書を作成することもできます。 詳細な手順については、[署名とエンドポイント証明書の作成](#create-signed-endpoint-certificates)に関するページを参照してください。

4. 3 つの個別の証明書を作成する場合も 1 つの証明書を作成する場合も、証明書の種類ごとに提供されているガイダンスに従って、サブジェクト名 (SN) とサブジェクトの別名 (SAN) を指定します。 

### <a name="create-signing-chain-certificate"></a>署名チェーン証明書を作成する

管理者モードで実行されている Windows PowerShell を使用して、この証明書を作成します。 **この方法で作成された証明書は、開発またはテストの目的のみに使用してください。**

署名チェーン証明書は、1 回だけ作成する必要があります。 他のエンドポイント証明書は、この証明書を参照して署名を確認します。
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>署名付きエンドポイント証明書を作成する

管理者モードで実行されている Windows PowerShell を使用して、この証明書を作成します。

以下の例では、次のデバイスに対してエンドポイント証明書を作成します。
    - デバイス名: `DBE-HWDC1T2`
    - DNS ドメイン: `microsoftdatabox.com`

ご使用のデバイスの名前と DNS ドメインに置き換えて、デバイスの証明書を作成してください。
 
**BLOB エンドポイント証明書**

個人用ストアに BLOB エンドポイントの証明書を作成します。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager エンドポイント証明書**

個人用ストアに Azure Resource Manager エンドポイントの証明書を作成します。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**デバイスのローカル Web UI 証明書**

個人用ストアにデバイスのローカル Web UI の証明書を作成します。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**すべてのエンドポイント用の単一のマルチ SAN 証明書**

個人用ストアにすべてのエンドポイント用の単一の証明書を作成します。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

証明書を作成したら、次の手順として、Azure Stack Edge Pro デバイスに証明書をアップロードします


## <a name="upload-certificates"></a>証明書をアップロードする 

デバイス用に作成した証明書は、既定では、クライアントの **個人用ストア** に格納されます。 これらの証明書は、デバイスにアップロードできるように、クライアントで適切な形式のファイルにエクスポートする必要があります。

1. ルート証明書は、`.cer` 拡張子を使用した DER 形式としてエクスポートする必要があります。 詳細な手順については、[DER 形式での証明書のエクスポート](#export-certificates-as-der-format)に関するページを参照してください。
2. エンドポイント証明書は、秘密キーを含む *.pfx* ファイルとしてエクスポートする必要があります。 詳細な手順については、[秘密キーを含む *.pfx* ファイルとしての証明書のエクスポート](#export-certificates-as-pfx-format-with-private-key)に関するページを参照してください。 
3. 次に、ローカル Web UI の [証明書] ページの **[+ 証明書の追加]** オプションを使用して、ルート証明書とエンドポイント証明書をデバイスにアップロードします。 

    1. 最初にルート証明書をアップロードします。 ローカル Web UI で、 **[証明書] > [+ 証明書の追加]** に移動します。

        ![署名チェーン証明書を追加する 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. 次に、エンドポイント証明書をアップロードします。 

        ![署名チェーン証明書を追加する 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        *.pfx* 形式の証明書ファイルを選択し、証明書のエクスポート時に指定したパスワードを入力します。 Azure Resource Manager 証明書が適用されるまでに数分かかる場合があります。

        最初に署名チェーンが更新されていない場合、エンドポイント証明書をアップロードしようとすると、エラーが発生します。

        ![証明書の適用エラー](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        前に戻り、署名チェーン証明書をアップロードし、その後にエンドポイント証明書をアップロードし、適用します。

> [!IMPORTANT]
> デバイス名または DNS ドメインが変更された場合、新しい証明書を作成する必要があります。 次に、クライアント証明書とデバイス証明書を、新しいデバイス名と DNS ドメインを使用して更新する必要があります。 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>デバイスにアクセスするクライアントに証明書をインポートする

作成してデバイスにアップロードした証明書は、(デバイスにアクセスする) Windows クライアントの適切な証明書ストアにインポートする必要があります。

1. DER としてエクスポートしたルート証明書は、クライアント システムの **信頼されたルート証明機関** にインポートする必要があります。 詳細な手順については、[信頼されたルート証明機関ストアへの証明書のインポート](#import-certificates-as-der-format)に関するページを参照してください。

2. `.pfx` としてエクスポートしたエンドポイント証明書は、`.cer` 拡張子を使用した DER としてエクスポートする必要があります。 この `.cer` は、システムの **個人用証明書ストア** にインポートします。 詳細な手順については、[個人用証明書ストアへの証明書のインポート](#import-certificates-as-der-format)に関するページを参照してください。

### <a name="import-certificates-as-der-format"></a>DER 形式で証明書をインポートする

Windows クライアントに証明書をインポートするには、次の手順を実行します。

1. ファイルを右クリックし、 **[証明書のインストール]** を選択します。 このアクションにより、証明書のインポート ウィザードが開始されます。

    ![証明書のインポート 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. **[ストアの場所]** で **[ローカル マシン]** を選択し、 **[次へ]** を選択します。

    ![証明書のインポート 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. **[証明書をすべて次のストアに配置する]** を選択し、 **[参照]** を選択します。 

    - 個人用ストアにインポートするには、リモート ホストの個人用ストアに移動し、 **[次へ]** を選択します。

        ![証明書のインポート 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - 信頼されたストアにインポートするには、信頼されたルート証明機関に移動し、 **[次へ]** を選択します。

        ![証明書のインポート 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. **[完了]** を選択します。 インポートが成功したことを示すメッセージが表示されます。

### <a name="export-certificates-as-pfx-format-with-private-key"></a>秘密キーを含む .pfx 形式で証明書をエクスポートする

秘密キー付きの SSL 証明書を Windows コンピューターにエクスポートするには、次の手順を実行します。 

> [!IMPORTANT]
> これらの手順は、証明書の作成に使用したのと同じコンピューターで実行してください。 

1. *certlm.msc* を実行して、ローカル コンピューターの証明書ストアを起動します。

1. **[個人]** フォルダーをダブルクリックし、 **[証明書]** をダブルクリックします。

    ![証明書のエクスポート 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. バックアップする証明書を右クリックし、 **[すべてのタスク] > [エクスポート...]** を選択します。

    ![証明書のエクスポート 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. 証明書のエクスポート ウィザードに従い、証明書を .pfx ファイルにバックアップします。

    ![証明書のエクスポート 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. **[はい、秘密キーをエクスポートします]** を選択します。

    ![証明書のエクスポート 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. **[Include all certificates in certificate path if possible]\(可能な場合は証明書パスのすべての証明書を含める\)** 、 **[すべての拡張プロパティをエクスポートする]** 、 **[証明書のプライバシーを有効にする]** を選択します。 

    > [!IMPORTANT]
    > **[Delete Private Key option if export is successful]\(エクスポートが成功した場合に秘密キーを削除する (オプション)\)** は選択しないでください。

    ![証明書のエクスポート 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. パスワードを入力し、これを憶えておきます。 パスワードを確認入力します。 パスワードによって秘密キーが保護されます。

    ![証明書のエクスポート 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. 設定した場所にファイルを保存することを選択します。

    ![証明書のエクスポート 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. **[完了]** を選択します。

    ![証明書のエクスポート 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. エクスポートが成功したことを示すメッセージが表示されます。 **[OK]** を選択します。

    ![証明書のエクスポート 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

.pfx ファイルのバックアップが選択した場所に保存され、安全に保存するために移動または保管する準備ができました。


### <a name="export-certificates-as-der-format"></a>DER 形式で証明書をエクスポートする

1. *certlm.msc* を実行して、ローカル コンピューターの証明書ストアを起動します。

1. 個人用証明書ストアで、ルート証明書を選択します。 右クリックし、 **[すべてのタスク] > [エクスポート...]** を選択します。

    ![証明書の DER をエクスポートする 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. 証明書ウィザードが開きます。 形式として **[DER encoded binary X.509 (.cer)]** を選択します。 **[次へ]** を選択します。

    ![証明書の DER をエクスポートする 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. .cer 形式のファイルをエクスポートする場所を参照して選択します。

    ![証明書の DER をエクスポートする 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. **[完了]** を選択します。

    ![証明書の DER をエクスポートする 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>サポートされる証明書アルゴリズム

 Azure Stack Edge Pro デバイスでは、Rivest–Shamir–Adleman (RSA) 証明書のみサポートされています。 楕円曲線デジタル署名アルゴリズム (ECDSA) 証明書はサポートされていません。

 RSA 公開キーを含む証明書は、RSA 証明書と呼ばれます。 楕円曲線暗号 (ECC) 公開キーを含む証明書は、ECDSA (楕円曲線デジタル署名アルゴリズム) 証明書と呼ばれます。 


## <a name="view-certificate-expiry"></a>証明書の有効期限を確認する

独自の証明書を導入する場合、証明書は通常 1 年または 6 か月で期限切れになります。 証明書の有効期限を確認するには、デバイスのローカル Web UI の **[証明書]** ページにアクセスします。 特定の証明書を選択すると、その証明書の有効期限を確認できます。

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates]().-->

## <a name="next-steps"></a>次のステップ

[Azure Stack Edge Pro デバイスを配置する](azure-stack-edge-gpu-deploy-prep.md)