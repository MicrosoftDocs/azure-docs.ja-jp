---
title: Azure API Management の開発者ポータルの概要
titleSuffix: Azure API Management
description: API Management の開発者ポータルについて説明します。開発者ポータルは、API コンシューマーが API を確認することができるカスタマイズ可能な Web サイトです。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30487218fc95be75d22b5a9ea5a6dbc224ffd025
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074799"
---
# <a name="overview-of-the-developer-portal"></a>開発者ポータルの概要

開発者ポータルは、自動的に生成され、完全にカスタマイズ可能な、API のドキュメントが含まれる Web サイトです。 API コンシューマーはここで API を見つけ、使用方法を確認し、アクセスを要求し、試すことができます。

この記事では、API Management の開発者ポータルのセルフホステッド バージョンとマネージド バージョンの違いについて説明します。 また、よく寄せられる質問への回答も記載しています。

![API Management 開発者ポータル](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>従来のポータルから移行する

> [!IMPORTANT]
> 従来の開発者ポータルは非推奨となり、セキュリティ更新プログラムのみを受け取るようになりました。 2023 年 10 月に廃止され、すべての API Management サービスから削除されるまでは、通常どおりに引き続き使用できます。

新しい開発者ポータルへの移行については、[専用のドキュメント記事](developer-portal-deprecated-migration.md)で説明されています。

## <a name="customization-and-styling"></a>カスタマイズとスタイル設定

開発者ポータルは、ドラッグ アンド ドロップで操作できる組み込みのビジュアル エディターを使用して、カスタマイズおよびスタイル設定できます。 詳細については、[このチュートリアル](api-management-howto-developer-portal-customize.md)を参照してください。

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> 拡張性

API Management サービスには、常に最新の組み込みの **マネージド** 開発者ポータルが含まれています。 Azure portal インターフェイスからアクセスできます。

既定ではサポートされていないカスタム ロジックを使用して拡張する必要がある場合は、そのコードベースを変更できます。 ポータルのコードベースは、[GitHub リポジトリで入手できます][1]。 たとえば、サードパーティのサポート システムと統合する新しいウィジェットを実装できます。 新しい機能を実装する場合は、次のいずれかのオプションを選択できます。

- API Management サービスの外部で、実装後のポータルを **セルフホスト** します。 ポータルをセルフホストする場合、管理者が保守を行う必要があり、アップグレードの責任を負うことになります。 Azure サポートによる支援は、[リポジトリの Wiki セクション][2]に記載されているように、セルフホステッド ポータルの基本的なセットアップのみに限定されます。
- **マネージド** ポータルのコードベースに新機能をマージするように、API Management チームに対してプル要求を開きます。

拡張性に関する詳細および手順については、[GitHub リポジトリ][1]と[ウィジェットの実装に関するチュートリアル][3]を参照してください。 [マネージド ポータルのカスタマイズのチュートリアル](api-management-howto-developer-portal-customize.md)に関するページでは、ポータルの管理パネルについて説明しています。これは、**マネージド** バージョンと **セルフホステッド** バージョンで共通です。

## <a name="frequently-asked-questions"></a><a name="faq"></a>よく寄せられる質問

このセクションでは、この開発者ポータルに関してよく寄せられる一般的な質問に回答します。 セルフホステッド バージョンに関する質問については、[GitHub リポジトリの wiki セクション](https://github.com/Azure/api-management-developer-portal/wiki)を参照してください。

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> プレビュー バージョンのポータルから移行するにはどうすればよいですか。

最初に開発者ポータルのプレビュー バージョンを起動した場合、API Management サービスの既定のコンテンツのプレビュー バージョンをプロビジョニングしました。 一般公開バージョンでは既定のコンテンツが大幅に変更されています。 たとえば、既定のコンテンツのプレビュー バージョンには、ログイン ページに OAuth ボタンが含まれておらず、API を表示するためにさまざまなウィジェットが使用されており、開発者ポータル ページの構築は限られた機能で行われます。 コンテンツに違いがある場合でも、ポータルのエンジン (基になるウィジェットを含む) は、開発者ポータルを発行するたびに自動的に更新されます。

以前のバージョンのコンテンツに基づいてポータルを大幅にカスタマイズした場合、それをそのまま使用し続け、手動でポータルのページに新しいウィジェットを置くことができます。 それ以外の場合は、ポータルのコンテンツを新しい既定のコンテンツに置き換えることをお勧めします。

マネージド ポータルのコンテンツをリセットするには、 **[操作]** メニュー セクションの **[コンテンツのリセット]** を選択します。 この操作では、ポータルのすべてのコンテンツが削除され、新しい既定のコンテンツがプロビジョニングされます。 開発者ポータルのカスタマイズと変更内容がすべて失われます。 **この操作を元に戻すことはできません**。

![ポータル コンテンツのリセット](media/api-management-howto-developer-portal/reset-content.png)

セルフホステッド バージョンを使用している場合は、GitHub リポジトリの `scripts.v2/cleanup.bat` と `scripts.v2/generate.bat` スクリプトを実行して、既存のコンテンツを削除し、新しいコンテンツをプロビジョニングします。 必ず事前に、ポータルのコードを GitHub リポジトリから最新リリースにアップグレードしてください。

2019 年 11 月の一般提供のお知らせの後にポータルに初めてアクセスした場合は、新しい既定のコンテンツが既に機能しているため、これ以上の操作は必要ありません。

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>必要な機能がポータルでサポートされていない

[GitHub リポジトリ][1]で機能要求を開いたり、[不足している機能を自分で実装][3]したりすることができます。 詳細については、上記の「**拡張性**」セクションを参照してください。

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a>ポータルのデプロイを自動化するにはどうすればよいですか。

マネージド バージョンとセルフホステッド バージョンのどちらを使用している場合でも、REST API を通じて開発者ポータルのコンテンツにプログラムでアクセスし、管理することができます。

API については、[GitHub リポジトリの wiki セクション][2]に記載されています。 たとえばテスト環境から運用環境など、環境間でのポータル コンテンツの移行を自動化するために使用できます。 このプロセスの詳細については、GitHub にある[このドキュメント記事](https://aka.ms/apimdocs/migrateportal)を参照してください。

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>マネージド バージョンからセルフホステッド バージョンにどうやって移行できますか。

[GitHub の開発者ポータル リポジトリの Wiki セクション][2]にある詳細記事を参照してください。

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>1 つの API Management サービスに複数の開発者ポータルを含めることはできますか。

1 つのマネージド ポータルと複数のセルフホステッド ポータルを使用できます。 すべてのポータルのコンテンツは同じ API Management サービスに格納されるため、同じになります。 ポータルの外観と機能を区別する場合は、実行時にたとえば URL に基づいてページを動的にカスタマイズする独自のカスタム ウィジェットを使用して、セルフホストすることができます。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>ポータルは、Azure Resource Manager テンプレートをサポートしていますか。また、API Management DevOps リソース キットと互換性がありますか。

いいえ。

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>ポータルのコンテンツは、API Management のバックアップ/復元機能を使用して保存されていますか。

いいえ。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>マネージド ポータルの依存関係に対して追加の VNET 接続を有効にする必要がありますか。

ほとんどの場合、必要ありません。

API Management サービスが内部の VNet にある場合、開発者ポータルにはネットワーク内からしかアクセスできません。 管理エンドポイントのホスト名は、ポータルの管理インターフェイスにアクセスするために使用するコンピューターから、サービスの内部 VIP に解決される必要があります。 管理エンドポイントが DNS に登録されていることを確認してください。 設定に誤りがある場合は、次のエラーが表示されます: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`

API Management サービスが内部 VNet にあり、インターネットから Application Gateway 経由でアクセスする場合は、開発者ポータルと API Management の管理エンドポイントへの接続を有効にしてください。 Web アプリケーションのファイアウォール規則を無効にする必要がある場合があります。 詳細については、[このドキュメントの記事](api-management-howto-integrate-internal-vnet-appgateway.md)を参照してください。

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>カスタムの API Management ドメインを割り当てていますが、発行済みのポータルが正しく動作しません

ドメインを更新した後、変更を有効にするには、[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>ID プロバイダーを追加しましたが、ポータルに表示されません

(Azure AD、Azure AD B2C などの) ID プロバイダーを構成した後、変更を有効にするには[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。 開発者ポータルのページに OAuth ボタン ウィジェットが含まれていることを確認してください。

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>委任を設定しましたが、ポータルで使用されていません

委任を設定した後、変更を有効にするには、[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>API Management の構成に対するその他の変更が開発者ポータルに反映されていません

(VNet、サインイン、製品条項などの) ほとんどの構成変更には[ポータルの再発行](api-management-howto-developer-portal-customize.md#publish)が必要です。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> 対話型コンソールを使用すると、CORS エラーが発生します

対話型コンソールは、ブラウザーからクライアント側の API 要求を行います。 API に [CORS ポリシー](api-management-cross-domain-policies.md#CORS)を追加して、CORS の問題を解決します。

CORS ポリシーの状態は、Azure portal の API Management サービスの **[ポータルの概要]** セクションで確認できます。 警告ボックスは、ポリシーが存在しないか、構成が正しくないことを示します。

![CORS ポリシーの状態を確認できる場所を示すスクリーンショット。](media/api-management-howto-developer-portal/cors-azure-portal.png)

**[CORS を有効にする]** ボタンをクリックして、CORS ポリシーを自動的に適用します。

CORS は手動で有効にすることもできます。

1. 生成されたポリシー コードを表示するには、 **[手動でグローバル レベルに適用する]** リンクを選択します。
2. Azure portal で API Management サービスの **[API]** セクションにある **[すべての API]** に移動します。
3. **[受信処理]** セクションで **[</>]** アイコンを選択します。
4. XML ファイルの **[<inbound>]** セクションにポリシーを挿入します。 **<origin>** 値が、開発者ポータルのドメインと一致していることを確認します。

> [!NOTE]
> 
> API(s) スコープではなく Product スコープで CORS ポリシーを適用し、API でヘッダーを介してサブスクリプション キー認証を使用する場合、コンソールは正しく動作しません。
>
> ブラウザーは OPTIONS HTTP 要求を自動的に発行しますが、サブスクリプション キーを含むヘッダーがこの要求には含まれていません。 サブスクリプション キーがないため、API Management は OPTIONS 呼び出しを Product に関連付けることができず、したがって CORS ポリシーを適用できません。
>
> 回避策として、クエリ パラメーターでサブスクリプション キーを渡すことができます。

> [!NOTE]
> 
> 1 つの CORS ポリシーのみが実行されます。 複数の CORS ポリシーを指定した場合 (たとえば、API レベルとすべての API レベル)、対話型コンソールが期待どおりに動作しないことがあります。

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>開発者ポータルを編集するにはどのようなアクセス許可が必要ですか。

管理モードでポータルを開いたときに `Oops. Something went wrong. Please try again later.` エラーが表示される場合、必要なアクセス許可 (Azure RBAC) が不足している可能性があります。

従来のポータルでは、ユーザー管理者によるポータルへのアクセスを許可するためには、サービス スコープのアクセス許可 `Microsoft.ApiManagement/service/getssotoken/action` (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) が必要でした。 新しいポータルでは、スコープ `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` のアクセス許可 `Microsoft.ApiManagement/service/users/token/action` が必要です。

次の PowerShell スクリプトを使用して、必要なアクセス許可を持つロールを作成することができます。 `<subscription-id>` パラメーターを忘れずに変更してください。 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
作成したロールは、Azure portal の **[Access Control (IAM)]\(アクセス制御 (IAM)\)** セクションから任意のユーザーに付与できます。 このロールをユーザーに割り当てると、サービス スコープのアクセス許可が割り当てられます。 ユーザーは、サービスの *任意の* ユーザーに代わって SAS トークンを生成できるようになります。 最低限、このロールはサービスの管理者に割り当てる必要があります。 次の PowerShell コマンドは、不必要なアクセス許可をユーザーに付与することを避けるために、最も低いスコープでユーザー `user1` にロールを割り当てる方法を示しています。 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

ユーザーにアクセス許可を付与した後、新しいアクセス許可を有効にするには、ユーザーが Azure portal から一度サインアウトし、再びサインインする必要があります。

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>`Unable to start the portal. See if settings are specified correctly (...)` エラーが表示されます

このエラーは、`https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` に対する `GET` 呼び出しが失敗すると表示されます。 この呼び出しは、ポータルの管理インターフェイスによってブラウザーから発行されます。

API Management サービスが VNet 内にある場合は、VNet 接続に関する前出の質問を参照してください。

カスタム ドメインに割り当てられているがブラウザーで信頼されていない TLS/SSL 証明書が原因で呼び出しが失敗する場合もあります。 軽減策として、管理エンドポイントのカスタム ドメインを削除することができます。API Management は、信頼できる証明書を使用して既定のエンドポイントにフォールバックします。

### <a name="whats-the-browser-support-for-the-portal"></a>ポータルのブラウザー サポートとは何ですか？

| Browser                     | サポートされています       |
|-----------------------------|-----------------|
| Apple Safari                | 可<sup>1</sup> |
| Google Chrome               | 可<sup>1</sup> |
| Microsoft Edge              | 可<sup>1</sup> |
| Microsoft Internet Explorer | いいえ              |
| Mozilla Firefox             | 可<sup>1</sup> |

 <small><sup>1</sup> 2 つの最新の生産バージョンでサポートされています。</small>

## <a name="next-steps"></a>次のステップ

新しい開発者ポータルの詳細を確認します:

- [マネージド開発者ポータルへのアクセスとカスタマイズ](api-management-howto-developer-portal-customize.md)
- [ポータルのセルフホステッド バージョンの設定][2]
- [独自のウィジェットの実装][3]

その他のリソースを参照します:

- [ソース コードがある GitHub リポジトリ][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
