---
title: "ポイント対サイトの自己署名証明書の作成: PowerShell: Azure | Microsoft Docs"
description: "この記事では、自己署名ルート証明書の作成、公開キーのエクスポート、クライアント証明書の生成を Windows 10 で PowerShell を使用して行う手順について説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 20fb95341d6240b883e711cf771c33f6d8978cb9
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-self-signed-root-certificate-for-point-to-site-connections-using-powershell"></a>ポイント対サイト接続用の自己署名ルート証明書を PowerShell を使用して作成する

ポイント対サイト接続では、認証に証明書を使用します。 ポイント対サイト接続を構成するときに、ルート証明書の公開キー (.cer ファイル) を Azure にアップロードする必要があります。 この記事は、自己署名ルート証明書の作成、公開キーのエクスポート、クライアント証明書の生成とインストールを行う際に役立ちます。

> [!NOTE]
> 以前は、自己署名ルート証明書を作成してポイント対サイト接続用のクライアント証明書を生成する手段として makecert が推奨されていました。 現在は、PowerShell を使用してそれらの証明書を作成できるようになっています。 PowerShell を使う利点は、SHA-2 証明書を作成できることです。 
>
>

## <a name="rootcert"></a>自己署名ルート証明書の作成

次の手順では、PowerShell を使用して自己署名ルート証明書を作成する方法を説明します。 次の手順を実行するには、Windows 10 が必要です。 これらの手順で使用されているコマンドレットとパラメーターは、Windows 10 オペレーティング システムの一部であり、PowerShell バージョンの一部ではありません。

1. Windows 10 を実行しているコンピューターから、昇格された特権を使用して Windows PowerShell コンソールを開きます。
2. 次の例を使用して、自己署名ルート証明書を作成します。 次の例では、"P2SRootCert" という名前の自己署名ルート証明書が作成され、"Certificates-Current User\Personal\Certificates" に自動的にインストールされます。 *certmgr.msc* を開くと、証明書を表示できます。

        $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign

### <a name="cer"></a>公開キーを取得するには

ポイント対サイト接続では、公開キー (.cer) が Azure にアップロードされている必要があります。 次の手順で、自己署名ルート証明書の .cer ファイルをエクスポートしてください。

1. 証明書から .cer ファイルを取得するには、 **certmgr.msc**を開きます。 自己署名ルート証明書を探して右クリックします (通常は 'Current User\Personal\Certificates' にあります)。 **[すべてのタスク]**、**[エクスポート]** の順にクリックします。 **証明書のエクスポート ウィザード**が開きます。
2. ウィザードで **[次へ]** をクリックします。 **[いいえ、秘密キーをエクスポートしません]** を選択して、**[次へ]** をクリックします。
3. **[エクスポート ファイルの形式]** ページで **[Base-64 encoded X.509 (.CER)]** を選択し、**[次へ]** をクリックします。 
4. **[エクスポートするファイル]** で、**[参照]** をクリックして証明書をエクスポートする場所を選択します。 **[ファイル名]**に証明書ファイルの名前を指定します。 その後、 **[次へ]**をクリックします。
5. **[完了]** をクリックして、証明書をエクスポートします。 "**エクスポートに成功しました**" というメッセージが表示されます。 **[OK]** をクリックしてウィザードを閉じます。

### <a name="to-export-a-self-signed-root-certificate-optional"></a>自己署名ルート証明書をエクスポートするには (省略可能)
自己署名ルート証明書をエクスポートし、安全に保管することもできます。 必要に応じて、後から別のコンピューターにインストールして、さらに多くのクライアント証明書を生成したり、別の .cer ファイルをエクスポートしたりできます。

自己署名ルート証明書を .pfx としてエクスポートするには、ルート証明書を選択し、「[クライアント証明書をエクスポートする](#clientexport)」と同じ手順を実行してエクスポートします。

## <a name="clientcert"></a>クライアント証明書の生成

ポイント対サイトで VNet に接続するすべてのクライアント コンピューターには、クライアント証明書がインストールされている必要があります。 自己署名ルート証明書からクライアント証明書を生成し、そのクライアント証明書をエクスポートしてインストールします。 クライアント証明書がインストールされていない場合は、認証が失敗します。 

次の手順では、自己署名ルート証明書からクライアント証明書を生成する方法を示しています。 同じルート証明書から複数のクライアント証明書を生成できます。 以下の手順を使用してクライアント証明書を生成すると、証明書の生成に使用したコンピューターにクライアント証明書が自動的にインストールされます。 クライアント証明書を別のクライアント コンピューターにインストールする場合は、その証明書をエクスポートできます。

次の手順を実行するには、Windows 10 が必要です。 これらの手順で使用されているコマンドレットとパラメーターは、Windows 10 オペレーティング システムの一部であり、PowerShell バージョンの一部ではありません。

### <a name="example-1"></a>例 1

この例では、前のセクションで宣言した '$cert' 変数を使用します。 自己署名ルート証明書の作成後に PowerShell コンソールを閉じた場合または新しい PowerShell コンソール セッションで追加のクライアント証明書を作成している場合は、例 2 の手順を使用してください。

クライアント証明書を生成するには、例を変更して実行します。 次の例を変更せずに実行した場合、クライアント証明書の名前は "P2SChildCert" になります。  子証明書に別の名前を付ける場合は、CN 値を変更します。 この例を実行する際に TextExtension を変更しないでください。 生成したクライアント証明書は、コンピューターの "Certificates - Current User\Personal\Certificates" に自動的にインストールされます。

    New-SelfSignedCertificate -Type Custom -KeySpec Signature `
    -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 -KeyLength 2048 `
    -CertStoreLocation "Cert:\CurrentUser\My" `
    -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

### <a name="example-2"></a>例 2

追加のクライアント証明書を作成している場合、または自己署名ルート証明書の作成に使用したのと同じ PowerShell セッションを使用していない場合は、次の手順を使用してください。

1. コンピューターにインストールされている自己署名ルート証明書を特定します。 次のコマンドレットは、コンピューターにインストールされている証明書の一覧を返します。

        Get-ChildItem -Path “Cert:\CurrentUser\My”

2. 返された一覧でサブジェクト名を探し、その横にある拇印をテキスト ファイルにコピーします。 次の例では、2 つの証明書があります。 CN 名は、子証明書の生成元となる自己署名ルート証明書の名前です。 この場合は "P2SRootCert" です。

        Thumbprint                                Subject

        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. 前の手順の拇印を使用して、ルート証明書の変数を宣言します。 THUMBPRINT を、子証明書の生成元となるルート証明書の拇印に置き換えます。

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"

    たとえば、前の手順の P2SRootCert の拇印を使用すると、変数は次のようになります。

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
        

4.  クライアント証明書を生成するには、例を変更して実行します。 次の例を変更せずに実行した場合、クライアント証明書の名前は "P2SChildCert" になります。 子証明書に別の名前を付ける場合は、CN 値を変更します。 この例を実行する際に TextExtension を変更しないでください。 生成したクライアント証明書は、コンピューターの "Certificates - Current User\Personal\Certificates" に自動的にインストールされます。

        New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" `
        -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

## <a name="clientexport"></a>クライアント証明書のエクスポート   

クライアント証明書を生成すると、このクライアント証明書は、生成に使用したコンピューターに自動的にインストールされます。 別のクライアント コンピューターにクライアント証明書をインストールする場合は、生成したクライアント証明書をエクスポートする必要があります。                              

1. クライアント証明書をエクスポートするには、 **certmgr.msc**を開きます。 生成したクライアント証明書は、既定では "Certificates - Current User\Personal\Certificates" にあります。 エクスポートするクライアント証明書を右クリックして、**[すべてのタスク]**、**[エクスポート]** の順にクリックします。 **証明書のエクスポート ウィザード**が開きます。
2. ウィザードで **[次へ]** をクリックし、**[はい、秘密キーをエクスポートします]** を選択して、**[次へ]** をクリックします。
3. **[エクスポート ファイルの形式]** ページでは、既定値をそのまま使用します。 [証明のパスにある証明書を可能であればすべて含む] がオンになっていることを確認します。 その後、 **[次へ]**をクリックします。
4. **[セキュリティ]** ページでは、秘密キーを保護する必要があります。 パスワードを使用する場合は、この証明書に設定したパスワードを必ず記録しておくか、覚えておいてください。 その後、 **[次へ]**をクリックします。
5. **[エクスポートするファイル]** で、**[参照]** をクリックして証明書をエクスポートする場所を選択します。 **[ファイル名]**に証明書ファイルの名前を指定します。 その後、 **[次へ]**をクリックします。
6. **[完了]** をクリックして、証明書をエクスポートします。    

## <a name="install"></a>エクスポートしたクライアント証明書のインストール

クライアント証明書の生成に使用したクライアント コンピューター以外から P2S 接続を作成する場合は、クライアント証明書をインストールする必要があります。 クライアント証明書をインストールするときに、クライアント証明書のエクスポート時に作成されたパスワードが必要になります。

1. *.pfx* ファイルを見つけ、クライアント コンピューターにコピーします。 クライアント コンピューターで、 *.pfx* ファイルをダブルクリックしてインストールします。 **[ストアの場所]** は **[現在のユーザー]** のままにしておき、**[次へ]** をクリックします。
2. **[インポートするファイル]** ページでは、何も変更しないでください。 **[次へ]**をクリックします。
3. **[秘密キーの保護]** ページで、証明書にパスワードを使用した場合はそのパスワード入力するか、証明書をインストールするセキュリティ プリンシパルが正しいことを確認し、**[次へ]** をクリックします。
4. **[証明書ストア]** ページで、既定の場所をそのまま使用し、**[次へ]** をクリックします。
5. **[完了]**をクリックします。 証明書のインストールの **[セキュリティ警告]** で **[はい]** をクリックします。 証明書を生成したので、[はい] をクリックしても問題ありません。 これで証明書がインポートされます。

## <a name="next-steps"></a>次のステップ
引き続きポイント対サイト構成を使用します。 

* **Resource Manager** デプロイメント モデルの手順については、[VNet へのポイント対サイト接続の構成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関する記事を参照してください。 
* **クラシック** デプロイメント モデルの手順については、[VNet へのポイント対サイト VPN 接続の構成 (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md) に関する記事を参照してください。


