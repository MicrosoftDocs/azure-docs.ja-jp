---
title: Azure Key Vault 証明書を使用した TLS 終端
description: HTTPS 対応リスナーにアタッチされているサーバー証明書の Key Vault と Azure Application Gateway を統合する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 8a64956deb7849568e70e94c9b58170df60db1e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775742"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Key Vault 証明書を使用した TLS 終端

[Azure Key Vault](../key-vault/general/overview.md) はプラットフォームマネージド シークレット ストアです。シークレット、キー、TLS または SSL 証明書を保護するために使用できます。 Azure Application Gateway では、HTTPS 対応リスナーにアタッチされているサーバー証明書用の Key Vault との統合をサポートします。 このサポートは、Application Gateway の v2 SKU に制限されます。

Key Vault 統合では、TLS または SSL 終端に次の 2 つのモデルが用意されています。

- リスナーにアタッチされている TLS または SSL 証明書を明示的に指定できます。 このモデルは、TLS 終端のために TLS または SSL 証明書を Application Gateway に渡す従来の方法を示しています。
- HTTPS 対応リスナーの作成時に、必要に応じて、既存の Key Vault 証明書またはシークレットへの参照を指定できます。

Application Gateway と Key Vault の統合には、次のようにさまざまな利点があります。

- セキュリティの強化 (TLS または SSL 証明書がアプリケーション開発チームによって直接処理されないため)。 統合により、別のセキュリティ チームが以下のこと実行できます。
  * アプリケーション ゲートウェイを設定する。
  * アプリケーション ゲートウェイのライフ サイクルを制御する。
  * ご使用のキー コンテナーに格納されている証明書にアクセスする権限を特定のアプリケーション ゲートウェイに付与する。
- ご使用のキー コンテナーに既存の証明書をインポートできます。 また、任意の信頼できる Key Vault パートナーと共に Key Vault API を使用して、新しい証明書を作成したり管理したりできます。
- ご使用のキー コンテナーに格納されている証明書を自動更新できます。

Application Gateway で現在サポートされているのは、ソフトウェアで検証された証明書のみです。 ハードウェア セキュリティ モジュール (HSM) で検証された証明書はサポートされません。 Key Vault 証明書を使用するように Application Gateway を構成すると、そのインスタンスによって Key Vault から証明書が取得され、TLS 終端のためにその証明書がローカルにインストールされます。 また、インスタンスによって Key Vault が 4 時間間隔でポーリングされ、証明書の更新バージョン (存在する場合) が取得されます。 更新された証明書が検出されると、HTTPS リスナーに現在関連付けられている TLS または SSL 証明書が自動的にローテーションされます。

> [!NOTE]
> Azure portal では、シークレットではなく KeyVault 証明書のみがサポートされます。 Application Gateway は引き続き KeyVault からのシークレットの参照をサポートしていますが、PowerShell、CLI、API、ARM テンプレートなどの非ポータル リソースを通じてのみとなります。 

## <a name="how-integration-works"></a>統合のしくみ

Application Gateway と Key Vault との統合には、3 つの手順の構成プロセスが必要です。

1. **ユーザー割り当てマネージド ID を作成する**

   Application Gateway がユーザーに代わって Key Vault から証明書を取得するために使用する、ユーザー割り当てマネージド ID を作成するか、既存の ID を使用します。 詳しくは、「[Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)」をご覧ください。 この手順では、Azure Active Directory テナントに新しい ID を作成します。 この ID は、ID を作成するために使用されたサブスクリプションによって信頼されます。

1. **キー コンテナーの構成**

   次に、既存の証明書をインポートするか、キー コンテナーで新しい証明書を作成します。 証明書は、アプリケーション ゲートウェイを介して実行されるアプリケーションによって使用されます。 この手順では、Base-64 でエンコードされたパスワードレスの PFX ファイルも格納できる Key Vault シークレットを使用することもできます。 Key Vault では "証明書" 型のオブジェクトに自動更新機能を使用できるため、この型を使用することをお勧めします。 証明書またはシークレットを作成したら、Key Vault でアクセス ポリシーを定義し、その ID にシークレットの取得アクセス権が付与されるようにする必要があります。
   
   > [!IMPORTANT]
   > 2021 年 3 月 15 日以降、Key Vault は Azure アプリケーション ゲートウェイを信頼されたサービスの 1 つとして認識するため、Azure でセキュリティで保護されたネットワーク境界を構築することができます。 これにより、すべてのネットワーク (インターネット トラフィックを含む) から Key Vault にアクセスするトラフィックを拒否することができますが、サブスクリプションで Application Gateway リソースにアクセスすることはできます。 

   > 次の方法で、Key Vault の制限されたネットワークで Application Gateway を構成できます。 <br />
   > a) Key Vault の [ネットワーク] ブレードで、 <br />
   > b) [ファイアウォールと仮想ネットワーク] タブでプライベート エンドポイントと選択したネットワークを選択し、 <br/>
   > c) 仮想ネットワークを使用して、Application Gateway の仮想ネットワークとサブネットを追加します。 このプロセス中に、チェックボックスをオンにして "Microsoft KeyVault" サービス エンドポイントも構成します。 <br/>
   > d) 最後に [はい] を選択し、信頼されたサービスが Key Vault のファイアウォールをバイパスできるようにします。 <br/>
   > 
   > ![Key Vault ファイアウォール](media/key-vault-certs/key-vault-firewall.png)


   > [!NOTE]
   > Azure CLI または PowerShell を使用して ARM テンプレート経由で、あるいは Azure portal からデプロイされた Azure アプリケーション経由でアプリケーション ゲートウェイをデプロイする場合、SSL 証明書は Base-64 でエンコードされた PFX ファイルとしてキー コンテナーに格納されます。 「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)」の手順を実行する必要があります。 
   >
   > 特に、`enabledForTemplateDeployment` を `true` に設定することが重要です。 証明書は、パスワードレスとすることも、パスワードを設定することもできます。 パスワードが設定された証明書の場合、次の例では、アプリ ゲートウェイの ARM テンプレート構成に対する `properties` の `sslCertificates` エントリの構成を示します。 `appGatewaySSLCertificateData` と `appGatewaySSLCertificatePassword` の値は、「[動的 ID でのシークレットの参照](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id)」セクションで説明されているように、キー コンテナーから検索されます。 参照がどのように行われるかを確認するには、`parameters('secretName')` から後方に参照をたどります。 証明書がパスワードレスである場合は、`password` エントリを省略します。
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **アプリケーション ゲートウェイの構成**

   前の 2 つの手順を完了したら、ユーザー割り当てマネージド ID を使用するようにプリケーション ゲートウェイを設定するか、既存のアプリケーション ゲートウェイを変更することができます。 詳細については、「[Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity)」を参照してください。

   また、キー コンテナーの証明書またはシークレットの ID の完全な URI をポイントするように、HTTP リスナーの TLS または SSL 証明書を構成します。

   ![キー コンテナー証明書](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>次のステップ

[Azure PowerShell で Key Vault 証明書を使用して TLS 終端を構成する](configure-keyvault-ps.md)
