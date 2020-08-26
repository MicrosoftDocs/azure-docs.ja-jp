---
title: Azure Key Vault に対する認証
description: Azure Key Vault に対して認証を行う方法について説明します
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7645600a476a1c2294ddd4a24fe01e2ffe51d5ac
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589984"
---
# <a name="authenticate-to-azure-key-vault"></a>Azure Key Vault に対する認証

## <a name="overview"></a>概要

Azure Key Vault はシークレット管理ソリューションであり、アプリケーション シークレットのストレージを一元化し、その配布を制御できます。 Azure Key Vault を使用すると、アプリケーションに資格情報を保存する必要がなくなります。 アプリケーションは、キー コンテナーに対して認証を行うことで必要な資格情報を取得できます。 このドキュメントでは、キー コンテナーに対する認証の基本について説明します。

このドキュメントは、キー コンテナーの認証のしくみを理解するのに役立ちます。 このドキュメントでは、認証フローについて説明し、キー コンテナーへのアクセス権を付与する方法を紹介します。また、キー コンテナーに格納されているシークレットをサンプルの Python アプリケーションから取得するためのチュートリアルも含まれています。

このドキュメントでは、以下について説明します。

* 主要概念
* セキュリティ プリンシパルの登録
* Key Vault 認証フローを理解する
* サービス プリンシパルに Key Vault へのアクセス権を付与する
* チュートリアル (Python)

## <a name="key-concepts"></a>主要概念

### <a name="azure-active-directory-concepts"></a>Azure Active Directory の概念

* Azure Active Directory (AAD) - Azure Active Directory (Azure AD) は、Microsoft のクラウドベースの ID およびアクセスの管理サービスであり、従業員によるリソースへのサインインとアクセスを支援します。

* ロールの定義 - ロールの定義は、アクセス許可のコレクションです。  AAD には、Azure リソースに対する読み取り、書き込み、削除などの操作を実行するためのアクセス許可のレベルを含む標準ロール (所有者、共同作成者、または閲覧者) が用意されています。 また、ロールには、ユーザーが作成した、特定の詳細なアクセス許可が割り当てられたカスタム定義を指定することもできます。

* アプリケーションの登録 - Azure AD アプリケーションを登録すると、Azure AD テナントには、アプリケーション オブジェクトとサービス プリンシパル オブジェクトという 2 つのオブジェクトが作成されます。 アプリケーション オブジェクトはすべてのテナントにわたって使用するためにアプリケーションをグローバルに表現したもの、サービス プリンシパル オブジェクトは特定のテナントで使用するためにアプリケーションをローカルで表現したものと考えることができます。

### <a name="security-principal-concepts"></a>セキュリティ プリンシパルの概念

* セキュリティ プリンシパル - セキュリティ プリンシパルとは、Azure リソースへのアクセスを要求しているユーザー、グループ、サービス プリンシパル、またはマネージド ID を表すオブジェクトです。

* ユーザー - Azure Active Directory 内にプロファイルを持つ個人です。

* グループ - Azure Active Directory 内に作成されたユーザーのセットです。 グループにロールを割り当てると、そのグループ内のすべてのユーザーがそのロールを持つようになります。

* サービス プリンシパル - 特定の Azure リソースにアクセスするためにアプリケーションまたはサービスによって使用されるセキュリティ ID です。 アプリケーションに対するユーザー ID (ユーザー名とパスワード、または証明書) と考えることができます。

* マネージド ID - Azure によって自動的に管理される、Azure Active Directory 内の ID。

* オブジェクト ID (クライアント ID) - Azure AD によって生成される一意識別子で、初回プロビジョニング時にサービス プリンシパルに関連付けられます。

## <a name="security-principal-registration"></a>セキュリティ プリンシパルの登録

1. 管理者がユーザーまたはアプリケーション (サービス プリンシパル) を Azure Active Directory に登録します。

2. 管理者が Azure キー コンテナーを作成し、アクセス ポリシー (ACL) を構成します。

3. (オプション) 管理者が Azure Key Vault ファイアウォールを構成します。

![イメージ](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>Key Vault 認証フローを理解する

1. サービス プリンシパルは、AAD に対する認証を行うための呼び出しを行います。これは、いくつかの方法で行うことができます。
    * ユーザーは、ユーザー名とパスワードを使用して Azure portal にログインできます。
    * アプリケーションは、クライアント ID を使用し、AAD に対してクライアント シークレットまたはクライアント証明書を提示します。
    * 仮想マシンなどの Azure リソースは、MSI が割り当てられているため、IMDS REST エンドポイントにアクセスしてアクセス トークンを取得します。

2. AAD に対する認証が成功すると、サービス プリンシパルに OAuth トークンが付与されます。
3. サービス プリンシパルが Key Vault を呼び出します。
4. Azure Key Vault ファイアウォールで、呼び出しを許可するかどうかが判定されます。
    * シナリオ 1:Key Vault ファイアウォールが無効で、キー コンテナーのパブリック エンドポイント (URI) にパブリック インターネットから到達可能です。 呼び出しは許可されます。
    * シナリオ 2: 呼び出し元は、Azure Key Vault の信頼できるサービスです。 このオプションが選択されている場合、特定の Azure サービスはキー コンテナー ファイアウォールをバイパスできます。 [Key Vault の信頼できるサービスの一覧](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * シナリオ 3: 呼び出し元は、IP アドレス、仮想ネットワーク、またはサービス エンドポイントで Azure Key Vault ファイアウォールに一覧表示されています。
    * シナリオ 4: 呼び出し元は、構成されたプライベート リンク接続を介して Azure Key Vault に到達できます。
    * シナリオ 5: 呼び出し元は承認されず、禁止応答が返されます。
5. Key Vault は、サービス プリンシパルのアクセス トークンを確認するための呼び出しを AAD に対して行います。
6. 要求された操作を実行するための十分なアクセス ポリシー アクセス許可がサービス プリンシパルにあるかどうかが Key Vault によって確認されます。この例では、操作はシークレットの取得です。
7. Key Vault によって、サービス プリンシパルにシークレットが提供されます。

![イメージ](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>サービス プリンシパルに Key Vault へのアクセス権を付与する

1. サービス プリンシパルがない場合は作成します。 [サービス プリンシパルを作成する](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Azure Key Vault の IAM 設定で、サービス プリンシパルにロールの割り当てを追加します。 事前に割り当てられたロールである所有者、共同作成者、または閲覧者を追加できます。 また、対象のサービス プリンシパル用にカスタム ロールを作成することもできます。 最小限の特権の原則に従い、対象のサービス プリンシパルに最低限必要なアクセス権のみを提供する必要があります。 
3.  Key Vault ファイアウォールを構成します。 Key Vault ファイアウォールを無効のままにし、パブリック インターネットからのアクセスを許可することができます (安全性は低くなりますが、構成が簡単になります)。 また、特定の IP 範囲、サービス エンドポイント、仮想ネットワーク、またはプライベート エンドポイントにアクセスを制限することもできます (安全性が高まります)。
4.  対象のサービス プリンシパルのアクセス ポリシーを追加します。これは、対象のサービス プリンシパルがキー コンテナーに対して実行できる操作の一覧です。 最小限の特権の原則を使用して、サービス プリンシパルが実行できる操作を制限する必要があります。 ただし、アクセス許可が不十分な場合、対象のサービス プリンシパルはアクセスを拒否されます。

## <a name="tutorial"></a>チュートリアル

このチュートリアルでは、Key Vault に対する認証を行ってシークレットを取得するようにサービス プリンシパルを構成する方法について学習します。 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>パート 1: Azure portal でサービス プリンシパルを作成する

1. Azure Portal にログインする
1. Azure Active Directory を検索する
1. [アプリの登録] タブをクリックする
1. [+ 新規登録] をクリックする
1. サービス プリンシパルの名前を作成する
1. [登録] を選択します

この時点で、サービス プリンシパルは登録されています。 これを表示するには、[アプリの登録] を選択します。 対象のサービス プリンシパルにはクライアント ID の GUID が割り当てられます。これは、サービス プリンシパルの "ユーザー名" と考えてください。 次に、対象のサービス プリンシパルの "パスワード" を作成する必要があります。クライアント シークレットまたはクライアント証明書を使用できます。 認証にクライアント シークレットを使用することは安全ではないため、テスト目的でのみ使用するよう注意してください。 このチュートリアルでは、クライアント証明書の使用方法について説明します。

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>パート 2: サービス プリンシパルのクライアント証明書を作成する

1. 証明書を作成する

    * オプション 1: [OpenSSL](https://www.openssl.org/) を使用して証明書を作成します (テスト専用。運用環境では自己署名証明書を使用しないでください)

    ```console
    openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
    ```

    * オプション 2:キー コンテナーを使用して証明書を作成します。 [Azure Key Vault で証明書を作成する](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. 証明書を PEM 形式でダウンロードします
1. Azure portal にログインし、Azure Active Directory に移動します
1. [アプリの登録] をクリックします
1. パート 1 で作成したサービス プリンシパルを選択します。
1. 対象のサービス プリンシパルの [Certificates and Secrets]\(証明書とシークレット\) タブをクリックします
1. [証明書のアップロード] ボタンを使用して証明書をアップロードします

### <a name="part-3-configure-an-azure-key-vault"></a>パート 3: Azure キー コンテナーを構成する

1. Azure キー コンテナーを作成します。[リンク](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)

2. キー コンテナーの IAM アクセス許可を構成します
    1. お使いのキー コンテナーに移動する
    1. [アクセス制御 (IAM)] タブを選択します
    1. [Add Role Assignment]\(ロールの割り当ての追加\) をクリックします
    1. ドロップダウンから [共同作成者] ロールを選択します
    1. 作成したサービス プリンシパルの名前またはクライアント ID を入力します
    1. [ロールの割り当ての表示] をクリックして、対象のサービス プリンシパルが表示されていることを確認します

3. Key Vault アクセス ポリシーのアクセス許可を構成します
    1. お使いのキー コンテナーに移動する
    1. [設定] で [アクセス ポリシー] タブを選択します
    1. [+ アクセス ポリシーの追加] リンクを選択します
    1. [シークレットのアクセス許可] ドロップダウンで、[取得] と [リスト] アクセス許可のチェック ボックスをオンにします。
    1. 名前またはクライアント ID で対象のサービス プリンシパルを選択します。
    1. [追加] を選択します
    1. [保存] を選択します

4. 対象のキー コンテナーにシークレットを作成します。
    1. お使いのキー コンテナーに移動する
    1. [設定] で [シークレット] タブをクリックします
    1. [+ Generate/Import]\(+ 生成/インポート\) をクリックします
    1. シークレットの名前を作成します。この例では、シークレットに "test" という名前を付けます
    1. シークレットの値を作成します。この例では、値 "password123" を設定します

これで、ローカル コンピューターからコードを実行すると、クライアント ID と証明書へのパスを提示してアクセス トークンを取得することで、キー コンテナーに対する認証を行うことができます。

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>パート 4:アプリケーションで Azure キー コンテナーからシークレットを取得する (Python)

次のコード サンプルを使用して、対象のアプリケーションで、構成したサービス プリンシパルを使用してキー コンテナーからシークレットを取得できるかどうかをテストします。 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![イメージ](../media/authentication-3.png)


## <a name="next-steps"></a>次の手順

1. キー コンテナー認証エラーのトラブルシューティング方法について学習します。 [Key Vault トラブルシューティング ガイド](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
