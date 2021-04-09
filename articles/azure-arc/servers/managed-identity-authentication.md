---
title: Arc 対応サーバーでの Azure リソースに対する認証
description: この記事では、Azure Instance Metadata Service での Arc 対応サーバーのサポートと、シークレットを使用して Azure リソースおよびローカルに対して認証する方法について説明します。
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96939044"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Arc 対応サーバーでの Azure リソースに対する認証

Azure Arc 対応サーバーで直接実行されるアプリケーションやプロセスは、マネージド ID を利用して、Azure Active Directory ベースの認証をサポートしている他の Azure リソースにアクセスできます。 アプリケーションはその ID を表す[アクセス トークン](../../active-directory/develop/developer-glossary.md#access-token) (Arc 対応サーバ-に対してシステムが割り当てる) を取得し、それをベアラー トークンとして使用して、別のサービスに対してそれ自体を認証できます。

マネージド ID の詳細な説明、およびシステム割り当ての ID とユーザー割り当ての ID の違いについては、[マネージド ID の概要](../../active-directory/managed-identities-azure-resources/overview.md)に関するドキュメントを参照してください。

この記事では、サーバーでシステム割り当てのマネージド ID を使用して Azure [Key Vault](../../key-vault/general/overview.md) にアクセスする方法について説明します。 ブートストラップとして機能する Key Vault により、クライアント アプリケーションは、Azure Active Directory (AD) で保護されていないリソースにシークレットを使用してアクセスできます。 たとえば、IIS Web サーバーで使用される TLS/SSL 証明書を Azure Key Vault に格納し、Azure 外部の Windows または Linux サーバーに証明書を安全にデプロイすることができます。

## <a name="security-overview"></a>セキュリティの概要

サーバーを Azure Arc 対応サーバーにオンボードする間、Azure VM の場合と同様に、マネージド ID を使用して構成するいくつかのアクションが実行されます。

- Azure Resource Manager は、Arc 対応サーバー上でシステム割り当てマネージド ID を有効にするための要求を受け取ります。

- Azure Resource Manager は、サーバーの ID を表すサービス プリンシパルを Azure AD に作成します。 このサブスクリプションによって信頼されている Azure AD テナントに、サービス プリンシパルが作成されます。

- Azure Resource Manager では、[Windows](../../virtual-machines/windows/instance-metadata-service.md) または [Linux](../../virtual-machines/linux/instance-metadata-service.md) 用の Azure Instance Metadata Service (IMDS) の ID エンドポイントをサービス プリンシパルのクライアント ID と証明書を使用して更新することによって、サーバー上に ID が構成されます。 このエンドポイントは、既知のルーティング不可の IP アドレスを使用してサーバーからのみアクセスできる、REST エンドポイントです。 このサービスは、Arc 対応サーバーを管理および構成するのに役立つ、メタデータ情報のサブセットを提供します。

マネージド ID が有効になっているサーバーの環境は、Windows の Arc 対応サーバー上で次の変数を使用して構成されます。

- **IMDS_ENDPOINT**: Arc 対応サーバーの IMDS エンドポイント IP アドレス `http://localhost:40342` です。

- **IDENTITY_ENDPOINT**: サービスのマネージド ID に対応する localhost エンドポイントです (`http://localhost:40342/metadata/identity/oauth2/token`)。

サーバー上で実行されているコードは、サーバー内からのみアクセスできる Azure Instance Metadata サービス エンドポイントにトークンを要求できます。

システム環境変数 **IDENTITY_ENDPOINT** は、アプリケーションが ID エンドポイントを検出するのに使用されます。 アプリケーションは **IDENTITY_ENDPOINT** と **IMDS_ENDPOINT** の値を取得して、それらを使用する必要があります。 あらゆるアクセス レベルのアプリケーションが、エンドポイントへの要求を行うことができます。 メタデータの応答は、通常どおりに処理され、コンピューター上の任意のプロセスに渡されます。 ただし、トークンを公開する要求が行われた場合は、上位の特権を持つユーザーのみが使用できるデータにアクセスできることを証明するためのシークレットを、クライアントが提供する必要があります。

## <a name="prerequisites"></a>前提条件

- マネージド ID の知識。
- Arc 対応サーバーに接続され、登録されているサーバー。
- 必要なリソース作成とロール管理の手順を実行するための、サブスクリプションまたはリソース グループの[所有者グループ](../../role-based-access-control/built-in-roles.md#owner)** のメンバーであること。
- 資格情報を格納および取得するための Azure Key Vault。 Azure Arc ID アクセスを KeyVault に割り当てます。

    - Key Vault が作成されていない場合は、「[Key Vault の作成](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-)」を参照してください。
    - サーバーが使用するマネージド ID でアクセスを構成する方法については、[Linux の場合のアクセス権の付与](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access)、または [Windows の場合のアクセス権の付与](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access)に関する記事を参照してください。 手順 5 では、Arc 対応サーバーの名前を入力します。 PowerShell を使用してこれを行う方法については、[PowerShell を使用してアクセス ポリシーを割り当てる](../../key-vault/general/assign-access-policy-powershell.md)方法に関する記事を参照してください。

## <a name="acquiring-an-access-token-using-rest-api"></a>REST API を使用してアクセス トークンを取得する

システムによって割り当てられたマネージド ID を取得し、それを使用して Azure リソースに対する認証を行う方法は、Azure VM での実行方法と似ています。

Arc 対応の Windows サーバーの場合は、PowerShell を使用し、Web 要求を呼び出して、特定のポートのローカル ホストからトークンを取得します。 IP アドレスまたは環境変数 **IDENTITY_ENDPOINT** を使用して要求を指定します。

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

次に示すのは、返される応答の例です。

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="PowerShell を使用したアクセス トークンの正常な取得。":::

Arc 対応の Linux サーバーの場合は、Bash を使用し、Web 要求を呼び出して、特定のポートのローカル ホストからトークンを取得します。 IP アドレスまたは環境変数 **IDENTITY_ENDPOINT** を使用して、次の要求を指定します。 この手順を完了するには、SSH クライアントが必要です。

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

次に示すのは、返される応答の例です。

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Bash を使用したアクセス トークンの正常な取得。":::

応答には、Azure のリソースへのアクセスに必要なアクセス トークンが含まれています。 Azure Key Vault に対して認証するための構成を完了する方法については、[Windows の場合の Key Vault へのアクセス](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data)または [Linux の場合の Key Vault へのアクセス](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Key Vault の詳細については、「[Key Vault の概要](../../key-vault/general/overview.md)」を参照してください。

- [PowerShell を使用して](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)、または[Azure CLI を使用して](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)リソースにマネージド ID アクセスを割り当てる方法を確認してください。