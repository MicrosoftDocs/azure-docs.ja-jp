---
title: ILB ASE v1 を作成する
description: 内部ロード バランサー (ILB ASE) を使用して、App Service Environment を作成します。 このドキュメントは、レガシ v1 ASE を使用するお客様にのみ提供されます。
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 2a03b791f37868010e107214ddcb7cf42174e4e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85833555"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して ILB ASE を作成する方法

> [!NOTE] 
> この記事は、App Service Environment v1 に関するものです。 より強力なインフラストラクチャ上で実行できる、使いやすい新しいバージョンの App Service Environment があります。 新しいバージョンの詳細については、「[App Service Environment の概要](intro.md)」を参照してください。
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>概要
App Service Environment は、パブリック VIP の代わりに仮想ネットワークの内部アドレスを使用して作成することができます。  この内部アドレスは、内部ロード バランサー (ILB) と呼ばれる Azure コンポーネントによって提供されます。  ILB ASE は、Azure ポータルを使用して作成できます。  また、Azure Resource Manager テンプレートによる自動化を利用して作成することもできます。  この記事では、Azure Resource Manager テンプレートを使用して ILB ASE を作成するために必要な手順と構文について詳しく説明します。

ILB ASE の自動作成は、次に示す 3 つの手順で実行されます。

1. まず、パブリック VIP の代わりに内部ロード バランサーのアドレスを使用し、仮想ネットワーク内にベース ASE が作成されます。  この手順には、ルート ドメイン名を ILB ASE に割り当てる操作も含まれます。
2. ILB ASE の作成が完了すると、TLS/SSL 証明書がアップロードされます。  
3. アップロードされた TLS/SSL 証明書は、"既定の" TLS/SSL 証明書として ILB ASE に明示的に割り当てられます。  この TLS/SSL 証明書は、ILB ASE 上のアプリが ASE に割り当てられた共通ルート ドメイン (`https://someapp.mycustomrootcomain.com` など) を使用してアドレス指定されている場合に、そのアプリへの TLS トラフィックに使用されます。

## <a name="creating-the-base-ilb-ase"></a>ベース ILB ASE の作成
Azure Resource Manager テンプレートの例と、それに関連するパラメーター ファイルは、[こちら][quickstartilbasecreate]から GitHub で入手できます。

*azuredeploy.parameters.json* ファイルのパラメーターのほとんどは、ILB ASE と、パブリック VIP にバインドされた ASE の両方の作成に共通するパラメーターです。  以下の一覧では、ILB ASE を作成するうえで特に注意が必要なパラメーターや、固有のパラメーターについて説明します。

* *internalLoadBalancingMode*:ほとんどの場合、これは 3 に設定します。この設定により、ポート 80/443 の HTTP/HTTPS トラフィックと、ASE 上の FTP サービスによってリッスンされているコントロール/データ チャネル ポートは、ILB が割り当てられた仮想ネットワークの内部アドレスにバインドされます。  このプロパティを 2 に設定すると、FTP サービス関連のポート (コントロール チャネルとデータ チャネル両方のポート) のみが ILB アドレスにバインドされ、HTTP/HTTPS トラフィックにはパブリック VIP が使用されます。
* *dnsSuffix*:このパラメーターでは、ASE に割り当てられる既定のルート ドメインを定義します。  Azure App Service のパブリック版では、すべての Web アプリの既定のルート ドメインは *azurewebsites.net* です。  しかし、ILB ASE はユーザーの仮想ネットワーク内部に位置することから、パブリック サービスの既定のルート ドメインを使用しても意味がありません。  ILB ASE には、会社の内部仮想ネットワーク内での使用に適した既定のルート ドメインを用意する必要があります。  たとえば、Contoso Corporation という架空の企業では、Contoso の仮想ネットワーク内でのみ解決およびアクセス可能になるよう設計されたアプリに対して、 *internal-contoso.com* という既定のルート ドメインが使用されます。 
* *ipSslAddressCount*:ILB ASE には単一の ILB アドレスしかないため、このパラメーターは、*azuredeploy.json* ファイル内で既定値の 0 に自動設定されます。  ILB ASE 用に明示されている IP-SSL アドレスがないため、ILB ASE の IP-SSL アドレス プールはゼロに設定する必要があり、ゼロ以外に設定するとプロビジョニング エラーが発生します。 

ILB ASE に関して *azuredeploy.parameters.json* ファイルへの入力が完了したら、以下の PowerShell コード スニペットを使用して ILB ASE を作成することができます。  ファイル パスは、マシン上の Azure Resource Manager テンプレート ファイルの場所に一致するように変更してください。  また、Azure Resource Manager のデプロイ名とリソース グループ名に独自の値を指定することも忘れずに行ってください。

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Azure Resource Manager テンプレートを送信した後、ILB ASE が作成されるまでには数時間かかります。  作成が完了すると、ポータル UX で、デプロイを開始したサブスクリプションの App Service Environment の一覧に ILB ASE が表示されます。

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>"既定の" TLS/SSL 証明書のアップロードと構成
ILB ASE を作成したら、TLS/SSL 証明書を、アプリへの TLS/SSL 接続を確立するために使用する "既定の" TLS/SSL証明書として ASE に関連付ける必要があります。  ここでも架空の Contoso Corporation の例で考えると、ASE の既定の DNS サフィックスが *internal-contoso.com* である場合、 *`https://some-random-app.internal-contoso.com`* への接続には * *.internal-contoso.com* に対して有効な TLS/SSL 証明書が必要です。 

有効な TLS/SSL 証明書を取得するには、内部 CA を利用する、外部の発行元から証明書を購入する、自己署名証明書を使用するなどの方法があります。  どのようなソースから TLS/SSL 証明書を取得した場合でも、以下の証明書属性を適切に構成する必要があります。

* *Subject*:この属性は、* *.your-root-domain-here.com* に設定する必要があります
* *Subject Alternative Name*:この属性には、* *.your-root-domain-here.com* と * *.scm.your-root-domain-here.com* の両方が含まれている必要があります。  2 つ目のエントリが必要な理由は、各アプリに関連付けられた SCM/Kudu サイトへの TLS 接続が、*your-app-name.scm.your-root-domain-here.com* 形式のアドレスを使用して確立されるためです。

有効な TLS/SSL 証明書を取得した後、さらに 2 つの準備手順を実行する必要があります。  TLS/SSL 証明書は、.pfx ファイルに変換して保存する必要があります。  .pfx ファイルには、中間証明書とルート証明書をすべて含める必要があり、さらにパスワードで保護する必要もあるという点に注意してください。

その後、.pfx ファイルは base64 文字列に変換する必要がありますが、これは TLS/SSL 証明書が Azure Resource Manager テンプレートを使用してアップロードされるためです。  Azure Resource Manager テンプレートはテキスト ファイルであるため、パラメーターとしてテンプレートに含めることができるように、.pfx ファイルを base64 文字列に変換する必要があります。

以下の PowerShell コード スニペットは、自己署名証明書を生成して、その証明書を .pfx ファイルとしてエクスポートし、.pfx ファイルを base64 でエンコードされた文字列に変換してから、それを別のファイルに保存する例を示しています。  base64 エンコード用の PowerShell コードは、[PowerShell スクリプトのブログ][examplebase64encoding]に記載されているコードを基にしています。

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

TLS/SSL 証明書が正常に生成され、base64 でエンコードされた文字列に変換されたら、GitHub にある、[既定の TLS/SSL 証明書を構成する][configuringDefaultSSLCertificate]ためのサンプルの Azure Resource Manager テンプレートを使用できます。

*azuredeploy.parameters.json* ファイル内の各パラメーターを以下に示します。

* *appServiceEnvironmentName*:構成対象の ILB ASE の名前。
* *existingAseLocation*:ILB ASE のデプロイ先の Azure リージョンを含むテキスト文字列。  次に例を示します。"South Central US"。
* *pfxBlobString*:based64 でエンコードされた .pfx ファイルの文字列表現。  前述したコード スニペットを使用すると、"exportedcert.pfx.b64" 内に含まれる文字列をコピーし、 *pfxBlobString* 属性の値として貼り付けることになります。
* *password*:pfx ファイルの保護に使用されるパスワード。
* *certificateThumbprint*:証明書のサムプリント。  この値を PowerShell (前述のコード スニペットにある *$certificate.Thumbprint* など) から取得している場合は、値をそのまま使用できます。  ただし、この値を Windows 証明書のダイアログからコピーした場合は、忘れずに余分なスペースを削除してください。  *certificateThumbprint* は、AF3143EB61D43F6727842115BB7F17BBCECAECAE のようになります
* *certificateName*:証明書の識別に使用される、独自に選択したわかりやすい文字列識別子。  この名前は、TLS/SSL 証明書を表す *Microsoft.Web/certificates* エンティティに固有の Azure Resource Manager 識別子の一部として使用されます。  この名前は、サフィックス "\_yourASENameHere_InternalLoadBalancingASE" で終わる **必要があります**。  このサフィックスは、ILB が有効な ASE を保護するためにこの証明書が使用されることを示すインジケーターとして、ポータルによって使用されます。

一部省略した *azuredeploy.parameters.json* の例を次に示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appServiceEnvironmentName": {
            "value": "yourASENameHere"
        },
        "existingAseLocation": {
            "value": "East US 2"
        },
        "pfxBlobString": {
            "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
        },
        "password": {
            "value": "PASSWORDGOESHERE"
        },
        "certificateThumbprint": {
            "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
        },
        "certificateName": {
            "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
        }
    }
}
```

*azuredeploy.parameters.json* ファイルへの入力が完了したら、以下の PowerShell コード スニペットを使用して既定の TLS/SSL 証明書を構成することができます。  ファイル パスは、マシン上の Azure Resource Manager テンプレート ファイルの場所に一致するように変更してください。  また、Azure Resource Manager のデプロイ名とリソース グループ名に独自の値を指定することも忘れずに行ってください。

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Azure Resource Manager テンプレートを送信した後、変更が適用されるまでには、ASE フロントエンドあたり約 40 分かかります。  たとえば、2 つのフロントエンドを使用する既定サイズの ASE では、テンプレートが完了するまで約 1 時間 20 分かかります。  テンプレートの実行中に、ASE をスケーリングすることはできません。  

テンプレートが完了すると、ILB ASE 上のアプリに HTTPS 経由でアクセスできるようになり、接続は既定の TLS/SSL 証明書を使用して保護されます。  既定の TLS/SSL 証明書は、ILB ASE 上のアプリが、アプリケーション名と既定のホスト名の組み合わせを使用してアドレス指定された場合に使用されます。  たとえば、 *`https://mycustomapp.internal-contoso.com`* の場合、* *.internal-contoso.com* の既定の TLS/SSL 証明書が使用されます。

ただし、パブリック マルチテナント サービスで実行されているアプリと同様に、開発者が個々のアプリに対してカスタム ホスト名を設定し、各アプリに固有の SNI TLS/SSL 証明書バインドを構成することもできます。  

## <a name="getting-started"></a>作業の開始
App Service Environment の使用を開始するには、「[App Service Environment の概要](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

