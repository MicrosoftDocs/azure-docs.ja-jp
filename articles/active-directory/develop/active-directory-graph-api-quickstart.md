---
title: "Azure AD Graph API のクイック スタート | Microsoft Docs"
description: "Azure Active Directory Graph API を使用すると、OData REST API エンドポイントを介して Azure AD にプログラムによってアクセスできます。 アプリケーションでは、Graph API を使用して、ディレクトリのデータとオブジェクトに対して、作成、読み取り、更新、および削除 (CRUD) の各操作を実行できます。"
services: active-directory
documentationcenter: n/a
author: viv-liu
manager: mbaldwin
editor: 
tags: 
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: fad5c315a247673b7a2ad52b4a78b49c567a997a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-for-the-azure-ad-graph-api"></a>Azure AD Graph API のクイック スタート
Azure Active Directory (AD) Graph API を使用すると、OData REST API エンドポイントを介して Azure AD にプログラムによってアクセスできます。 アプリケーションでは、Graph API を使用して、ディレクトリのデータとオブジェクトに対して、作成、読み取り、更新、および削除 (CRUD) の各操作を実行できます。 たとえば、Graph API を使用して、新しいユーザーの作成、ユーザーのプロパティの表示または更新、ユーザーのパスワードの変更、ロールベースでアクセスするためのグループ メンバーシップの確認、ユーザーの無効化または削除を行うことができます。 Graph API の機能とアプリケーション シナリオの詳細については、[Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) に関するページと [Azure AD Graph API の前提条件](https://msdn.microsoft.com/library/hh974476.aspx)に関するページを参照してください。 

> [!IMPORTANT]
> Azure Active Directory リソースにアクセスする場合、Azure AD Graph API ではなく [Microsoft Graph](https://developer.microsoft.com/graph) を使用することを強くお勧めします。 開発作業は現在 Microsoft Graph に集中しており、Azure AD Graph API の追加の機能強化は予定されていません。 Azure AD Graph API の使用が適切なシナリオの数は非常に限られています。詳しくは、Office デベロッパー センターのブログ投稿「[Microsoft Graph or the Azure AD Graph (Microsoft Graph または Azure AD Graph)](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph)」をご覧ください。
> 
> 

## <a name="how-to-construct-a-graph-api-url"></a>Graph API URL の作成方法
Graph API では、CRUD 操作を実行するディレクトリのデータとオブジェクト (つまり、リソースまたはエンティティ) にアクセスするために、Open Data (OData) プロトコルに基づく URL を使用できます。 Graph API で使用される URL は、4 つの主要部分で構成されます。主要部分は、サービス ルート、テナント ID、リソース パス、およびクエリ文字列オプション (https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]) です。`https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]` 次の URL の例を使用して説明します。`https://graph.windows.net/contoso.com/groups?api-version=1.6`

* **サービス ルート**: Azure AD Graph API では、サービス ルートは常に https://graph.windows.net です。
* **テナント ID**: このセクションでは、確認済み (登録済み) のドメイン名を指定できます。上記の例では contoso.com です。テナント オブジェクト ID またはエイリアス ("myorganization" または "me") を指定することもできます。 詳細については、[Graph API のエンティティと操作のアドレス指定](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)に関するページを参照してください。
* **リソース パス**: URL のこのセクションは、対話するリソース (ユーザー、グループ、特定のユーザー、特定のグループなど) を識別します。上記の例では、リソース セットのアドレスを指定する最上位の "groups" です。 特定のエンティティ ("users/{objectId}" や "users/userPrincipalName" など) のアドレスを指定することもできます。
* **クエリ パラメーター**: リソース パス セクションとクエリ パラメーター セクションは疑問符 (?) で区切られます。 Graph API ではすべての要求に "api-version" クエリ パラメーターが必要です。 Graph API では、OData クエリ オプションの **$filter**、**$orderby**、**$expand**、**$top**、および **$format** もサポートします。 クエリ オプションの **$count**、**$inlinecount**、および **$skip** は現在サポートされていません。 詳細については、「[Azure AD Graph API でサポートされているクエリ、フィルター、およびページング オプション](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options)」を参照してください。

## <a name="graph-api-versions"></a>Graph API のバージョン
“api-version” クエリ パラメーターに、Graph API 要求用のバージョンを指定します。 バージョン 1.5 以降の場合は、バージョン値を使用します (api-version=1.6)。 以前のバージョンでは、YYYY-MM-DD の形式に準拠した日付文字列を使用します (例: api-version=2013-11-08)。 機能をプレビューする場合は文字列 “beta” を使用します (例: api-version=beta)。 Graph API のバージョン間の相違点の詳細については、「 [Azure AD Graph API のバージョン](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning)」を参照してください。

## <a name="graph-api-metadata"></a>Graph API のメタデータ
Graph API メタデータ ファイルが返るようにするには、URL の tenant-identifier の後ろに "$metadata" セグメントを追加します。たとえば、次の URL では、デモ会社のメタデータが返されます`https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`。 この URL を Web ブラウザーのアドレス バーに入力して、メタデータを表示できます。 返される CSDL メタデータ ドキュメントには、要求した Graph API バージョンによって公開されているエンティティと複合型、それらのプロパティ、および関数とアクションが記述されています。 api-version パラメーターを省略すると、最新バージョンのメタデータが返されます。

## <a name="common-queries"></a>一般的なクエリ
[Azure AD Graph API の一般的なクエリ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries)に関するセクションに、Azure AD Graph で使用できる一般的なクエリの一覧が示されています。一般的なクエリには、ディレクトリ内の最上位リソースにアクセスするために使用できるクエリと、ディレクトリで操作を実行するためのクエリがあります。

たとえば、 `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` は、contoso.com ディレクトリの会社情報が返ります。

`https://graph.windows.net/contoso.com/users?api-version=1.6` は、contoso.com ディレクトリのすべてのユーザー オブジェクトを一覧表示します。

## <a name="using-the-graph-explorer"></a>Graph Explorer の使用
Azure AD Graph API 用の Graph Explorer を使用して、アプリケーションの作成時にディレクトリ データをクエリできます。

次に示す図は、Graph Explorer に移動し、サインインして、サインインしたユーザー ディレクトリのすべてのユーザーを表示するために「`https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6`」と入力した場合に表示される出力です。

![Azure AD Graph API Explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Graph Explorer を読み込む**: ツールを読み込むには、[https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/) に移動します。 **[ログイン]** をクリックし、Azure AD アカウント資格情報でサインインし、テナントに対して Graph Explorer を実行できます。 独自のテナントに対して Graph Explorer を実行する場合は、ユーザー自身またはユーザーの管理者がサインイン中に同意する必要があります。 Office 365 サブスクリプションがある場合は、Azure AD テナントが自動的に設定されます。 Office 365 にサインインするための資格情報は、実際は Azure AD アカウントであり、これらの資格情報を Graph Explorer で使用できます。

**クエリを実行する**:クエリを実行するには、要求テキスト ボックスにクエリを入力し、**[取得]** をクリックするか、**Enter** キーを押します。 結果が応答ボックスに表示されます。 たとえば、`https://graph.windows.net/myorganization/groups?api-version=1.6` は、サインインしたユーザー ディレクトリ内のすべてのグループ オブジェクトを一覧表示します。

Graph Explorer の次の機能と制限事項に注意してください。

* リソース セットのオートコンプリート機能。 この機能を確認するには、(会社の URL が表示されている) 要求テキスト ボックスをクリックします。 ドロップダウン リストからリソース セットを選択できます。
* アドレス指定のエイリアスとして “me” と “myorganization” の使用をサポートします。 たとえば、`https://graph.windows.net/me?api-version=1.6` を使用してサインインしているユーザーのユーザー オブジェクトを返し、`https://graph.windows.net/myorganization/users?api-version=1.6` を使用して現在のディレクトリのすべてのユーザーを返すことができます。
* 応答のヘッダー セクション。 このセクションはクエリの実行時に発生した問題の解決に役立ちます。
* 展開機能と折りたたみ機能がある応答用の JSON ビューアー。
* サムネイル写真の表示はサポートされません。

## <a name="using-fiddler-to-write-to-the-directory"></a>Fiddler を使用したディレクトリへの書き込み
このクイックスタート ガイドでは、Azure AD ディレクトリに対する「書き込み」操作を練習するために、Fiddler Web Debugger を使用できます。 Fiddler の詳細とインストールについては、 [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler)を参照してください。

次の例では、Fiddler Web Debugger を使用して、Azure AD ディレクトリに新しいセキュリティ グループ 'MyTestGroup' を作成します。

**アクセス トークンを取得する**: Azure AD Graph にアクセスするには、クライアントの Azure AD に対する認証が成功している必要があります。 詳細については、「 [Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)」を参照してください。

**クエリを構成して実行する**: 次の手順を完了します。

1. Fiddler Web Debugger を開き、 **[Composer]** タブに切り替えます。
2. 新しいセキュリティ グループを作成するため、プルダウン メニューから HTTP メソッドとして **[Post]** を選択します。 グループ オブジェクトに対する操作と権限の詳細については、[Azure AD Graph REST API リファレンス](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)の [Group](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) に関するセクションを参照してください。
3. **[Post (投稿)]** の横にあるフィールドに、要求 URL として「`https://graph.windows.net/mytenantdomain/groups?api-version=1.6`」と入力します。
   
   > [!NOTE]
   > mytenantdomain は、各自の Azure AD ディレクトリのドメイン名に置き換える必要があります。
   > 
   > 
4. [Post] プルダウンのすぐ下にあるフィールドに、次のように入力します。
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > &lt;your access token&gt; は、各自の Azure AD ディレクトリ用のアクセス トークンに置き換えます。
   > 
   > 
5. **[Request body]** フィールドに、次のように入力します。
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    グループ作成の詳細については、「 [グループの作成](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup)」を参照してください。

Graph によって公開される Azure AD エンティティと型の詳細と、それらに対して Graph を使用して実行できる操作に関する情報については、「 [Azure AD Graph REST API リファレンス](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* [Azure AD Graph API のアクセス許可スコープ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

