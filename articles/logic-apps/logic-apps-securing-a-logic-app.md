---
title: "Azure Logic Apps へのアクセスのセキュリティ保護 | Microsoft Docs"
description: "Azure Logic Apps のワークフローで使用されるトリガー、入出力、アクション パラメーター、サービスへのアクセスを保護するセキュリティを追加します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 86c293e735f766dbacc7d0b83574f254573d0de8
ms.openlocfilehash: 3f119409e031ca2b88694a011916f52aa9ef5d36
ms.lasthandoff: 02/15/2017


---

# <a name="secure-access-to-your-logic-apps"></a>ロジック アプリへのアクセスのセキュリティ保護

ロジック アプリのセキュリティ保護に役立つさまざまなツールを利用できます。

* ロジック アプリをトリガーするアクセス (HTTP 要求トリガー) のセキュリティ保護
* ロジック アプリの管理、編集、読み取りアクセスのセキュリティ保護
* 実行の入力および出力の内容に対するアクセスのセキュリティ保護
* ワークフローのアクション内のパラメーターまたは入力のセキュリティ保護
* ワークフローからの要求を受信するサービスに対するアクセスのセキュリティ保護

## <a name="secure-access-to-trigger"></a>トリガーへのアクセスのセキュリティ保護

HTTP 要求 ([要求](../connectors/connectors-native-reqres.md)または [Webhook](../connectors/connectors-native-webhook.md)) で起動するロジック アプリを使用する場合は、承認されたクライアントのみがロジック アプリを起動できるようにアクセスを制限できます。 ロジック アプリへのすべての要求は、SSL により暗号化され保護されます。

### <a name="shared-access-signature"></a>Shared Access Signature

ロジック アプリのすべての要求エンドポイントには、URL の一部として [Shared Access Signature (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md) が含まれます。 各 URL には、`sp`、`sv`、および `sig` クエリ パラメーターが含まれます。 アクセス許可の指定は `sp` によって行い、許可される HTTP メソッドに相当します。`sv` は生成に使用されるバージョンであり、`sig` はトリガーへのアクセスの認証に使用されます。 署名は、SHA256 アルゴリズムと秘密鍵を使用してすべての URL パスとプロパティで生成されます。 この秘密鍵が公開されることはなく、ロジック アプリの一部として暗号化されて格納されます。 ロジック アプリでは、秘密鍵を使って作成された有効な署名を含むトリガーのみが認証されます。

#### <a name="regenerate-access-keys"></a>アクセス キーを再生成する

セキュリティ キーは、REST API または Azure Portal を通じていつでも再生成できます。 以前に古いキーを使って生成された現在の URL はすべて無効になり、ロジック アプリの起動が承認されなくなります。

1. Azure Portal で、キーを再生成するロジック アプリを開きます。
1. **[設定]** の下にある **[アクセス キー]** メニュー項目をクリックします。
1. 再生成するキーを選択し、プロセスを完了します。

再生成後に取得した URL は、新しいアクセス キーで署名されます。

#### <a name="creating-callback-urls-with-an-expiration-date"></a>有効期限付きのコールバック URL を作成する

URL を他の関係者と共有している場合は、必要に応じて、キーと有効期限を指定して URL を生成できます。 その後、シームレスにキーを切り替えたり、アプリを起動するアクセスを一定期間に制限したりすることができます。 URL の有効期限は、[ロジック アプリの REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) で指定できます。

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

本文には、`NotAfter` プロパティを JSON 日付文字列として含めます。これにより、`NotAfter` の日時までに限り有効なコールバック URL が返されます。

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>プライマリまたはセカンダリの秘密鍵を使用して URL を作成する

要求ベースのトリガーに対するコールバック URL を生成または一覧表示する場合、URL の署名に使用するキーを指定することもできます。  特定のキーで署名した URL を生成するには、次のように[ロジック アプリの REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) を使用します。

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

本文では、`KeyType` プロパティに `Primary` または `Secondary` を指定します。  これにより、指定したセキュリティ キーによって署名された URL が返されます。

### <a name="restrict-incoming-ip-addresses"></a>受信 IP アドレスの制限

Shared Access Signature に加えて、ロジック アプリの呼び出しを特定のクライアントからのみに制限することもできます。  たとえば、Azure API Management でエンドポイントを管理すると、API Management インスタンスの IP アドレスから要求を受信した場合にのみ、ロジック アプリが要求を受け入れるように制限できます。

この設定は、ロジック アプリの設定内で構成できます。

1. Azure Portal で、IP アドレスの制限を追加するロジック アプリを開きます。
1. **[設定]** の **[Access control configuration] \(アクセス制御の構成)** メニュー項目をクリックします。
1. トリガーで受け入れる IP アドレス範囲の一覧を指定します。

有効な IP 範囲の形式は `192.168.1.1/255` です。 ロジック アプリを入れ子になったロジック アプリとしてのみ起動する場合は、**[Only other logic apps] \(他のロジック アプリのみ)** オプションをオンにします。 このオプションによって空の配列がリソースに書き込まれ、サービス自体 (親ロジック アプリ) からの呼び出しのみが正常に起動するようになります。

> [!NOTE]
> 要求トリガーのあるロジック アプリは、IP に関係なく引き続き REST API と Management の `/triggers/{triggerName}/run` で実行できます。 このシナリオでは Azure REST API に対する認証が必要であり、すべてのイベントが Azure の監査ログに表示されます。 アクセス制御ポリシーを適切に設定してください。

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>リソース定義で IP 範囲を設定する

[デプロイ テンプレート](logic-apps-create-deploy-template.md)を使ってデプロイを自動化する場合、リソース テンプレートで IP 範囲を設定できます。  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "triggers": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory、OAuth、またはその他のセキュリティの追加

ロジック アプリに認証プロトコルを追加できるように、[Azure API Management](https://azure.microsoft.com/services/api-management/) は、ロジック アプリを API として公開する機能に加えてあらゆるエンドポイント向けの監視、セキュリティ、ポリシー、およびドキュメントを備えています。 Azure API Management ではロジック アプリに対して、Azure Active Directory、証明書、OAuth などのセキュリティ標準を利用可能なパブリックまたはプライベート エンドポイントを公開できます。 要求を受信すると、Azure API Management が要求をロジック アプリに転送し、必要な変換や制限が転送中に行われます。 ロジック アプリの受信 IP 範囲の設定を使用して、ロジック アプリが API Management からのみトリガーされるようにすることができます。

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>ロジック アプリを管理または編集するアクセスのセキュリティ保護

ロジック アプリの管理操作へのアクセスを制限して、リソースに対する操作を特定のユーザーまたはグループのみが実行できるようにすることが可能です。 ロジック アプリでは Azure の[役割ベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) 機能が使用されており、同じツールでカスタマイズできます。  また、サブスクリプションのメンバーに割り当てることのできる組み込みの役割もいくつかあります。

* **ロジック アプリの共同作成者** - ロジック アプリを表示、編集、更新できるアクセス権を提供します。  リソースの削除や管理操作は実行できません。
* **ロジック アプリのオペレーター** - ロジック アプリと実行履歴の表示、および有効と無効の切り替えを行うことができます。  定義の編集や更新はできません。

[Azure のリソース ロック](../azure-resource-manager/resource-group-lock-resources.md)を使用して、ロジック アプリの変更や削除を防止することもできます。 この機能は、運用リソースが変更されたり削除されることを防ぐために役立ちます。

## <a name="secure-access-to-contents-of-the-run-history"></a>実行履歴の内容へのアクセスのセキュリティ保護

過去の実行の入力または出力の内容に対するアクセスを、特定の IP アドレス範囲に制限できます。  

ワークフロー実行内のすべてのデータは、転送中および保存中は暗号化されています。 実行履歴への呼び出しが行われると、サービスによって要求が認証され、要求および応答の入力と出力へのリンクが提供されます。 このリンクを保護して、指定した IP アドレス範囲からの内容を表示する要求のみが内容を返すようにすることができます。 この機能を使用するとアクセス制御を強化できます。 IP アドレスを `0.0.0.0` のように指定して、どのユーザーも入力および出力にアクセスできないようにすることもできます。 この制限を削除できるのは管理者アクセス許可を持つユーザーのみであるため、ワークフローの内容への "ジャストインタイム" アクセスが可能になります。

この設定は Azure Portal のリソース設定内で構成できます。

1. Azure Portal で、IP アドレスの制限を追加するロジック アプリを開きます。
1. **[設定]** の **[Access control configuration] \(アクセス制御の構成)** メニュー項目をクリックします。
1. 内容にアクセスする IP アドレス範囲の一覧を指定します。

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>リソース定義で IP 範囲を設定する

[デプロイ テンプレート](logic-apps-create-deploy-template.md)を使ってデプロイを自動化する場合、リソース テンプレートで IP 範囲を設定できます。  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "contents": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>ワークフロー内のパラメーターと入力のセキュリティ保護

複数環境でデプロイを行う場合、ワークフロー定義にはパラメーター化をした方がよい点がいくつかあります。 また、これらのパラメーターの中には、HTTP アクションの [Azure Active Directory 認証](../connectors/connectors-native-http.md#authentication)で使用されるクライアント ID やクライアント シークレットなど、ワークフローの編集時に表示しない方がよいセキュリティ保護されたパラメーターもあります。

### <a name="using-parameters-and-secure-parameters"></a>パラメーターとセキュリティ保護されたパラメータの使用

実行時にリソース パラメーターの値にアクセスするために、[ワークフロー定義言語](http://aka.ms/logicappsdocs)では `@parameters()` 操作を使用できます。 また、[リソース デプロイ テンプレートでパラメーターを指定](../azure-resource-manager/resource-group-authoring-templates.md#parameters)することもできます。 ただし、パラメーターの型を `securestring` に指定すると、パラメーターはリソース定義の残りの部分と一緒には返されなくなり、デプロイ後にリソースを表示してアクセスすることもできなくなります。

> [!NOTE]
> パラメーターを要求のヘッダーまたは本文で使用している場合、実行履歴と発信 HTTP 要求にアクセスすることでパラメーターが表示される場合があります。 コンテンツ アクセス ポリシーを適切に設定してください。
> Authorization ヘッダーは入力や出力では表示されません。 このため、このヘッダーでシークレットを使用する場合、シークレットを取得することはできません。

#### <a name="resource-deployment-template-with-secrets"></a>シークレットを含むリソース デプロイ テンプレート

実行時に `secret` のセキュリティ保護されたパラメーターを参照するデプロイの例を次に示します。 別のパラメーター ファイルで `secret` の環境値を指定するか、または [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) を使用してデプロイ時にシークレットを取得することもできます。

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secretDeploymentParam": {
      "type": "securestring"
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
        "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "actions": {
            "Call_External_API": {
              "type": "http",
              "inputs": {
                "headers": {
                  "Authorization": "@parameters('secret')"
                },
                "body": "This is the request"
              },
              "runAfter": {}
            }
          },
          "parameters": {
            "secret": {
              "type": "SecureString"
            }
          },
          "triggers": {
            "manual": {
              "type": "Request",
              "kind": "Http",
              "inputs": {
                "schema": {}
              }
            }
          },
          "contentVersion": "1.0.0.0",
          "outputs": {}
        },
        "parameters": {
          "secret": {
            "value": "[parameters('secretDeploymentParam')]"
          }
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>ワークフローからの要求を受信するサービスに対するアクセスのセキュリティ保護

ロジック アプリでアクセスする必要があるエンドポイントのセキュリティ保護に役立つさまざまな方法があります。

### <a name="using-authentication-on-outbound-requests"></a>送信要求に対する認証を使用する

HTTP、HTTP と Swagger (Open API)、または Webhook アクションを使用する場合は、送信される要求に認証を追加することができます。 追加できる認証には、基本認証、証明書認証、Azure Active Directory 認証があります。 この認証の構成方法の詳細については、[こちらの記事](../connectors/connectors-native-http.md#authentication)を参照してください。

### <a name="restricting-access-to-logic-app-ip-addresses"></a>ロジック アプリの IP アドレスへのアクセスを制限する

ロジック アプリからのすべての呼び出しは、リージョンごとの特定の IP アドレス セットから送信されます。 これらの専用 IP アドレスからの要求のみを受け入れるように、フィルターを追加できます。 これらの IP アドレスの一覧については、「[ロジック アプリの制限と構成](logic-apps-limits-and-config.md#configuration)」を参照してください。

### <a name="on-premises-connectivity"></a>オンプレミスの接続

ロジック アプリは、セキュリティで保護され信頼性の高いオンプレミス通信を実現する、いくつかのサービスとの統合機能を備えています。

#### <a name="on-premises-data-gateway"></a>オンプレミスのデータ ゲートウェイ

ロジック アプリの管理対象コネクタの多くは、オンプレミス システム (File System、SQL、SharePoint、DB2 など) にセキュリティで保護された接続を備えています。  ゲートウェイは Azure Service Bus 経由の暗号化されたチャネルを利用してオンプレミスにデータを中継するため、すべてのトラフィックの送信元は、ゲートウェイ エージェントのセキュリティ保護された送信トラフィックになります。  ゲートウェイのしくみの詳細については、[こちらの記事](logic-apps-gateway-install.md#how-the-gateway-works)を参照してください。

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) には、オンプレミス システムへのプロキシと通信のセキュリティ保護を実現するためのサイト間 VPN と ExpressRoute の統合など、オンプレミス接続オプションがあります。 ロジック アプリ デザイナーでは、Azure API Management から公開された API をワークフロー内ですばやく選択して、オンプレミス システムへ迅速にアクセスすることができます。

#### <a name="hybrid-connections-from-azure-app-service"></a>Azure App Services からのハイブリッド接続

Azure API と Web アプリでは、オンプレミスとの通信にハイブリッド接続機能を使用できます。  ハイブリッド接続と構成方法の詳細については、[こちらの記事](../app-service-web/web-sites-hybrid-connection-get-started.md)を参照してください。

## <a name="next-steps"></a>次のステップ
[デプロイ テンプレートを作成する](logic-apps-create-deploy-template.md)  
[例外処理](logic-apps-exception-handling.md)  
[ロジック アプリを監視する](logic-apps-monitor-your-logic-apps.md)  
[ロジック アプリの障害と問題の診断](logic-apps-diagnosing-failures.md)  

