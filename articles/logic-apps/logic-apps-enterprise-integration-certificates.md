---
title: 証明書を使用して B2B メッセージをセキュリティで保護する - Azure Logic Apps | Microsoft Docs
description: Enterprise Integration Pack を使用して、Azure Logic Apps の B2B メッセージをセキュリティで保護するために証明書を追加します
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 38bc1615c0849a33ddfa5790a66fc05d681ce339
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244931"
---
# <a name="secure-b2b-messages-with-certificates"></a>証明書を使用して B2B メッセージをセキュリティで保護する

B2B 通信の機密性を確保する必要がある場合は、統合アカウントに証明書を追加することで、エンタープライズ統合アプリ (具体的にはロジック アプリ) の B2B 通信をセキュリティで保護することができます。 証明書は、電子通信の参加者の ID を確認し、電子通信を次の方法で保護するためのデジタル ドキュメントです。

* メッセージの内容を暗号化する。
* メッセージにデジタル署名する。 

エンタープライズ統合アプリでは、次の証明書を使うことができます。

* [パブリック証明書](https://en.wikipedia.org/wiki/Public_key_certificate): これはパブリック インターネットの[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) から購入する必要がありますが、キーは必要ありません。 

* プライベート証明書または[*自己署名証明書*](https://en.wikipedia.org/wiki/Self-signed_certificate): これはお客様自身が作成して発行する必要がありますが、やはり秘密キーは不要です。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>パブリック証明書のアップロード

B2B 機能を備えたロジック アプリで "*パブリック証明書*" を使うには、最初に統合アカウントに証明書をアップロードする必要があります。 作成した[契約](logic-apps-enterprise-integration-agreements.md)でプロパティを定義した後は、証明書を使って B2B メッセージをセキュリティ保護できるようになります。

1. [Azure Portal](https://portal.azure.com) にサインインします。 Azure のメイン メニューで、**[すべてのリソース]** を選択します。 検索ボックスに統合アカウント名を入力し、目的の統合アカウントを選択します。

   ![統合アカウントを探して選択する](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. **[コンポーネント]** で、**[証明書]** タイルを選択します。

   ![[証明書] を選択する](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. **[証明書]** で **[追加]** を選びます。 **[証明書の追加]** で、証明書に関する下記の情報を指定します。 完了したら、**[OK]** を選びます。

   | プロパティ | 値 | 説明 | 
   |----------|-------|-------------|
   | **Name** | <*証明書名*> | 証明書の名前 (この例では "publicCert") | 
   | **証明書の種類** | パブリック | 証明書の種類 |
   | **証明書** | <*証明書ファイル名*> | アップロードする証明書ファイルを検索して選択するには、**[証明書]** ボックスの横にあるフォルダー アイコンを選択します。 |
   ||||

   ![[追加] を選択し、証明書の詳細を指定する](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   選択した内容が Azure によって確認されると、証明書がアップロードされます。

   ![新しい証明書が表示される](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>プライベート証明書のアップロード

B2B 機能を備えたロジック アプリで "*プライベート証明書*" を使うには、最初に統合アカウントに証明書をアップロードする必要があります。 また、最初に秘密キーを [Azure Key Vault](../key-vault/key-vault-get-started.md) に追加しておく必要があります。 

作成した[契約](logic-apps-enterprise-integration-agreements.md)でプロパティを定義した後は、証明書を使って B2B メッセージをセキュリティ保護できるようになります。

> [!NOTE]
> プライベート証明書の場合、メッセージの署名と暗号化のため [AS2 契約](logic-apps-enterprise-integration-as2.md)の**送信と受信**の設定に表示される、対応するパブリック証明書を追加する必要があります。

1. [Azure Key Vault に秘密キーを追加](../key-vault/certificate-scenarios.md#import-a-certificate)し、**[キー名]** を指定します。
   
2. Azure Logic Apps が Azure Key Vault で操作を実行することを承認します。 Logic Apps サービス プリンシパルへのアクセスを許可するには、次の例のように、PowerShell コマンド [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) を使います。

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. [Azure Portal](https://portal.azure.com) にサインインします。 Azure のメイン メニューで、**[すべてのリソース]** を選択します。 検索ボックスに統合アカウント名を入力し、目的の統合アカウントを選択します。

   ![統合アカウントの検索](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. **[コンポーネント]** で、**[証明書]** タイルを選択します。  

   ![[証明書] タイルを選ぶ](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. **[証明書]** で **[追加]** を選びます。 **[証明書の追加]** で、証明書に関する下記の情報を指定します。 完了したら、**[OK]** を選びます。

   | プロパティ | 値 | 説明 | 
   |----------|-------|-------------|
   | **Name** | <*証明書名*> | 証明書の名前 (この例では "privateCert") | 
   | **証明書の種類** | プライベート | 証明書の種類 |
   | **証明書** | <*証明書ファイル名*> | アップロードする証明書ファイルを検索して選択するには、**[証明書]** ボックスの横にあるフォルダー アイコンを選択します。 | 
   | **リソース グループ** | <*統合アカウントのリソース グループ*> | 統合アカウントのリソース グループ (この例では "MyResourceGroup") | 
   | **Key Vault** | <*キー コンテナー名*> | Azure キー コンテナーの名前 |
   | **キー名** | <*key-name*> | キーの名前 |
   ||||

   ![[追加] を選択し、証明書の詳細を指定する](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   選択した内容が Azure によって確認されると、証明書がアップロードされます。

   ![新しい証明書が表示される](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>次の手順

* [B2B 契約の作成](logic-apps-enterprise-integration-agreements.md)
