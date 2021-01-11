---
title: Azure API Management 開発者ポータルの概要
titleSuffix: Azure API Management
description: API Management の開発者ポータルについて説明します。 コンシューマーは、開発者ポータルで API を見つけることができます。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/28/2020
ms.author: apimpm
ms.openlocfilehash: 6a8c4c3fa2bd73fa689458d6877d09900ea86938
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852159"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API Management 開発者ポータルの概要

開発者ポータルは、自動的に生成され、完全にカスタマイズ可能な、API のドキュメントが含まれる Web サイトです。 API コンシューマーはここで API を見つけ、使用方法を確認し、アクセスを要求し、試すことができます。

この記事では、API Management の開発者ポータルのセルフホステッド バージョンとマネージド バージョンの違いについて説明します。 また、アーキテクチャについて説明し、よく寄せられる質問とその回答を紹介します。

![API Management 開発者ポータル](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> <a name="migrate-from-legacy"></a> 新しい開発者ポータルは、従来の開発者ポータルとは互換性がなく、自動移行はできません。 コンテンツ (ページ、テキスト、メディア ファイル) を手動で再作成し、新しいポータルの外観をカスタマイズする必要があります。 ガイダンスについては、[開発者ポータルのチュートリアル](api-management-howto-developer-portal-customize.md)を参照してください。

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>マネージド バージョンとセルフホステッド バージョン

開発者ポータルは 2 つの方法で構築できます。

- **マネージド バージョン** - API Management インスタンスに組み込まれたポータルを編集およびカスタマイズします。URL `<your-api-management-instance-name>.developer.azure-api.net` からアクセスできます。 マネージド ポータルにアクセスしてカスタマイズする方法については、[こちらのドキュメント記事](api-management-howto-developer-portal-customize.md)を参照してください。
- **セルフホステッド バージョン** - API Management インスタンスの外部にポータルをデプロイし、自らホスティングします。 このアプローチでは、ポータルのコードベースを編集し、提供されているコア機能を拡張することができます。たとえば、サードパーティ システムと統合するためのカスタムウィジェットを実装します。 このシナリオでは、あなたがポータルの管理者であり、ポータルを最新バージョンにアップグレードする責任があります。 詳細および手順については、[ポータルのソース コードを含む GitHub リポジトリ][1]と[ウィジェットの実装に関するチュートリアル][3]を参照してください。 [マネージド バージョンのチュートリアル](api-management-howto-developer-portal-customize.md)では、ポータルの管理パネルについて説明します。これは、セルフホステッド バージョンでも共通となります。

## <a name="portal-architectural-concepts"></a>ポータルのアーキテクチャの概念

ポータルのコンポーネントは、*コード*と*コンテンツ*の 2 つのカテゴリに論理的に分類することができます。

*コード*は [GitHub リポジトリ][1]で保持されており、次のものが含まれています。

- ウィジェット - ビジュアル要素を表します。HTML、JavaScript、スタイル設定機能、設定、およびコンテンツ マッピングが結合されます。 例としては、画像、テキスト段落、フォーム、API 一覧などがあります。
- スタイル定義 - ウィジェットのスタイル設定の方法を指定します。
- エンジン - ポータル コンテンツから静的 Web ページを生成します。JavaScript で記述されています。
- ビジュアル エディター - ブラウザー内でのカスタマイズおよび作成エクスペリエンスを可能にします。

*コンテンツ*は、*ポータル コンテンツ*と *API Management コンテンツ*の 2 つのサブカテゴリに分類されます。

*ポータル コンテンツ*はポータルに固有のものであり、次のものが含まれます。

- ページ - たとえば、ランディング ページ、API チュートリアル、ブログ投稿など
- メディア - 画像、アニメーション、およびその他のファイルベースのコンテンツ
- レイアウト - URL に対して照合され、ページの表示方法を定義するテンプレート
- スタイル - フォント、色、罫線などのスタイル定義の値
- 設定 - お気に入りアイコン、Web サイト メタデータなどの構成

*ポータル コンテンツ*は、メディアを除き、JSON ドキュメントとして表現されます。

*API Management コンテンツ*には、API、操作、製品、サブスクリプションなどのエンティティが含まれています。

ポータルは、[Paperbits フレームワーク](https://paperbits.io/)の適合されたフォークに基づいています。 元の Paperbits の機能が拡張され、API Management 固有のウィジェット (API の一覧、製品の一覧など)、およびコンテンツを保存および取得するための API Management サービスへのコネクタが提供されるようになりました。

## <a name="frequently-asked-questions"></a><a name="faq"></a>よく寄せられる質問

このセクションでは、この開発者ポータルに関してよく寄せられる一般的な質問に回答します。 セルフホステッド バージョンに関する質問については、[GitHub リポジトリの wiki セクション](https://github.com/Azure/api-management-developer-portal/wiki)を参照してください。

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> プレビュー バージョンのポータルから移行するにはどうすればよいですか。

開発者ポータルのプレビュー バージョンを使用して、API Management サービスのプレビュー コンテンツのプロビジョニングを行いました。 ユーザー エクスペリエンスを向上させるために、一般公開バージョンでは既定のコンテンツが大幅に変更されています。 また、新しいウィジェットも含まれています。

マネージド バージョンを使用している場合は、 **[操作]** メニュー セクションの **[コンテンツのリセット]** をクリックして、ポータルのコンテンツをリセットします。 この操作を確認すると、ポータルのすべてのコンテンツが削除され、新しい既定のコンテンツがプロビジョニングされます。 ポータルのエンジンは、API Management サービスで自動的に更新されています。

![ポータル コンテンツのリセット](media/api-management-howto-developer-portal/reset-content.png)

セルフホステッド バージョンを使用している場合は、GitHub リポジトリの `scripts/cleanup.bat` と `scripts/generate.bat` を使用して、既存のコンテンツを削除し、新しいコンテンツをプロビジョニングします。 必ず事前に、ポータルのコードを GitHub リポジトリから最新リリースにアップグレードしてください。

ポータルのコンテンツをリセットしたくない場合は、新しく利用可能となったウィジェットをページ全体で使用することを検討してください。 既存のウィジェットは、最新バージョンに自動的に更新されています。

一般公開の発表後にポータルをプロビジョニングした場合は、新しい既定のコンテンツが既に取り入れられているはずです。 ユーザー側の操作は必要ありません。

### <a name="does-the-portal-have-all-the-features-of-the-legacy-portal"></a>ポータルには、レガシ ポータルのすべての機能が含まれていますか。

この開発者ポータルでは、*アプリケーション* と *問題* はサポートされなくなりました。

### <a name="has-the-legacy-portal-been-deprecated"></a>レガシ ポータルは非推奨となりましたか。

以前の開発者ポータルと発行者ポータルは*レガシ*機能となりました。セキュリティ更新プログラムのみ受け取ることになります。 新しい機能は、新しい開発者ポータルにのみ実装されます。

レガシ ポータルの廃止については、あらためて発表されます。 質問、懸念事項、またはコメントがある場合は、[専用の GitHub の問題](https://github.com/Azure/api-management-developer-portal/issues/121)に提起してください。

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>必要な機能がポータルでサポートされていない

[機能要求](https://aka.ms/apimwish) を開いたり、[不足している機能を自分で実装][3] したりすることができます。 機能を自分で実装する場合は、開発者ポータルをセルフホストするか、GitHub で PR を開いて、変更内容をマネージバージョンに含めることができます。

### <a name="how-can-i-automate-portal-deployments"></a>ポータルのデプロイを自動化するにはどうすればよいですか。

マネージド バージョンとセルフホステッド バージョンのどちらを使用している場合でも、REST API を通じて開発者ポータルのコンテンツにプログラムでアクセスし、管理することができます。

API については、[GitHub リポジトリの wiki セクション][2]に記載されています。 たとえばテスト環境から運用環境など、環境間でのポータル コンテンツの移行を自動化するために使用できます。 このプロセスの詳細については、GitHub にある[このドキュメント記事](https://aka.ms/apimdocs/migrateportal)を参照してください。

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>マネージド バージョンからセルフホステッド バージョンにどうやって移行できますか。

[GitHub 上の開発者ポータル リポジトリの Wiki セクション][2]にある詳細記事を参照してください。

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>1 つの API Management サービスに複数の開発者ポータルを含めることはできますか。

1 つのマネージド ポータルと複数のセルフホステッド ポータルを使用できます。 すべてのポータルのコンテンツは同じ API Management サービスに格納されるため、同じになります。 ポータルの外観と機能を区別する場合は、実行時にたとえば URL に基づいてページを動的にカスタマイズする独自のカスタム ウィジェットを使用して、セルフホストすることができます。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>ポータルは、Azure Resource Manager テンプレートをサポートしていますか。また、API Management DevOps リソース キットと互換性がありますか。

いいえ。

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>ポータルのコンテンツは、API Management のバックアップ/復元機能を使用して保存されていますか。

いいえ。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>マネージド ポータルの依存関係に対して追加の VNET 接続を有効にする必要がありますか。

ほとんどの場合、必要ありません。

API Management サービスが内部の VNet にある場合、開発者ポータルにはネットワーク内からしかアクセスできません。 管理エンドポイントのホスト名は、ポータルの管理インターフェイスにアクセスするために使用するコンピューターから、サービスの内部 VIP に解決される必要があります。 管理エンドポイントが DNS に登録されていることを確認してください。 設定に誤りがある場合は、次のエラーが表示されます: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`

API Management サービスが内部 VNet にあり、インターネットから Application Gateway 経由でアクセスする場合は、開発者ポータルと API Management の管理エンドポイントへの接続を有効にしてください。

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>カスタムの API Management ドメインを割り当てていますが、発行済みのポータルが正しく動作しません

ドメインを更新した後、変更を有効にするには、[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>ID プロバイダーを追加しましたが、ポータルに表示されません

(AAD、AAD B2C などの) ID プロバイダーを構成した後、変更を有効にするには[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>委任を設定しましたが、ポータルで使用されていません

委任を設定した後、変更を有効にするには、[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>API Management の構成に対するその他の変更が開発者ポータルに反映されていません

(VNet、サインイン、製品条項などの) ほとんどの構成変更には[ポータルの再発行](api-management-howto-developer-portal-customize.md#publish)が必要です。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> 対話型コンソールを使用すると、CORS エラーが発生します

対話型コンソールは、ブラウザーからクライアント側の API 要求を行います。 API に [CORS ポリシー](api-management-cross-domain-policies.md#CORS)を追加して、CORS の問題を解決します。

CORS ポリシーの状態は、Azure portal の API Management サービスの **[ポータルの概要]** セクションで確認できます。 警告ボックスは、ポリシーが存在しないか、構成が正しくないことを示します。

![API Management 開発者ポータル](media/api-management-howto-developer-portal/cors-azure-portal.png)

**[CORS を有効にする]** ボタンをクリックして、CORS ポリシーを自動的に適用します。

CORS は手動で有効にすることもできます。

1. 生成されたポリシー コードを表示するには、 **[Manually apply it on the global level]\(手動でグローバル レベルに適用する\)** リンクをクリックします。
2. Azure portal で API Management サービスの **[API]** セクションにある **[すべての API]** に移動します。
3. **[受信処理]** セクションで **[</>]** アイコンをクリックします。
4. XML ファイルの **[\<inbound\>]** セクションにポリシーを挿入します。 **\<origin\>** 値が、開発者ポータルのドメインと一致していることを確認します。

> [!NOTE]
> 
> API(s) スコープではなく Product スコープで CORS ポリシーを適用し、API でヘッダーを介してサブスクリプション キー認証を使用する場合、コンソールは正しく動作しません。
>
> ブラウザーは OPTIONS HTTP 要求を自動的に発行しますが、サブスクリプション キーを含むヘッダーがこの要求には含まれていません。 サブスクリプション キーがないため、API Management は OPTIONS 呼び出しを Product に関連付けることができず、したがって CORS ポリシーを適用できません。
>
> 回避策として、クエリ パラメーターでサブスクリプション キーを渡すことができます。

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>開発者ポータルを編集するにはどのようなアクセス許可が必要ですか。

管理モードでポータルを開いたときに `Oops. Something went wrong. Please try again later.` エラーが表示される場合、必要なアクセス許可 (RBAC) が不足している可能性があります。

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
 
作成したロールは、Azure portal の **[Access Control (IAM)]\(アクセス制御 (IAM)\)** セクションから任意のユーザーに付与できます。 このロールをユーザーに割り当てると、サービス スコープのアクセス許可が割り当てられます。 ユーザーは、サービスの*任意の*ユーザーに代わって SAS トークンを生成できるようになります。 最低限、このロールはサービスの管理者に割り当てる必要があります。 次の PowerShell コマンドは、不必要なアクセス許可をユーザーに付与することを避けるために、最も低いスコープでユーザー `user1` にロールを割り当てる方法を示しています。 

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
