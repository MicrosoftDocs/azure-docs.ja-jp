---
title: Azure Active Directory 認証を使用した Azure Media Services API へのアクセス | Microsoft Docs
description: Azure Active Directory (Azure AD) を使用して Azure Media Services API へのアクセスを認証するための手順と概念を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 3688f0f9266844ce584f3f2d2e1a7c39ed3e4f70
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009017"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Azure AD 認証を使用した Azure Media Services API へのアクセス 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

Azure Media Services API は RESTful API です。 この API を使って、メディア リソースに対して操作を実行できます。そのためには、REST API または公開されているクライアント SDK を使用します。 Azure Media Services には、Microsoft .NET 用の Media Services クライアント SDK が用意されています。 Media Services リソースと Media Services API へのアクセスが承認されるには、まず認証を受ける必要があります。 

Media Services では、[Azure Active Directory (Azure AD) ベースの認証](../../active-directory/fundamentals/active-directory-whatis.md)がサポートされています。 Azure Media REST サービスでは、REST API 要求を行うユーザーまたはアプリケーションに、リソースにアクセスするための **共同作成者** ロールまたは **所有者** ロールが付与されている必要があります。 詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。  

このドキュメントでは、REST API または .NET API を使用して Media Services API にアクセスする方法の概要を説明します。

> [!NOTE]
> Access Control 承認は 2018 年 6 月 1 日に非推奨となりました。

## <a name="access-control"></a>アクセス制御

Azure Media REST 要求を成功させるには、呼び出すユーザーに、アクセスしたい Media Services アカウントの共同作成者ロールまたは所有者ロールが付与されている必要があります。  
所有者ロールが付与されているユーザーだけが、新しいユーザーまたはアプリにメディア リソース (アカウント) アクセス権を付与できます。 共同作成者ロールでは、メディア リソースにアクセスすることしかできません。
承認されていない要求は、状態コード 401 で失敗します。 このエラー コードが表示される場合、ユーザーの Media Services アカウントに共同作成者ロールと所有者ロールのいずれかが割り当てられていることを確認してください。 これは Azure Portal で確認できます。 メディア アカウントを探して、 **[アクセス制御]** タブをクリックします。 

![[アクセス制御] タブ](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>認証の種類 
 
Azure Media Services で Azure AD 認証を使用する場合、次の 2 つの認証オプションがあります。

- **ユーザー認証**。 Media Services リソースを操作するアプリを使用しているユーザーが認証を受けます。 ユーザーは最初に、対話型アプリケーションからユーザー資格情報の入力を求められます。 例として、承認済みユーザーがエンコード ジョブまたはライブ ストリーミングを監視するために使用する管理コンソール アプリがあります。 
- **サービス プリンシパル認証**。 サービスが認証を受けます。 この認証方法がよく使用されるアプリケーションは、デーモン サービス、中間層サービス、またはスケジュールされたジョブを実行するアプリです。 たとえば、Web アプリ、関数アプリ、ロジック アプリ、API、マイクロサービスなどです。

### <a name="user-authentication"></a>ユーザー認証 

このユーザー認証の方法が適したアプリケーションは、管理ネイティブ アプリまたは監視ネイティブ アプリです (例: モバイル アプリ、Windows アプリ、コンソール アプリケーション)。 この種のソリューションは、次のいずれかのシナリオで人によるサービスの操作が必要な場合に役立ちます。

- エンコード ジョブの監視ダッシュボード。
- ライブ ストリームの監視ダッシュボード。
- デスクトップ ユーザーまたはモバイル ユーザーが Media Services アカウント内のリソースを管理するための管理アプリケーション。

> [!NOTE]
> この認証方法は、コンシューマー向けアプリケーションでの使用に適していません。 

最初に、ネイティブ アプリケーションによって Azure AD からアクセス トークンが取得されます。次に、ユーザーが Media Services REST API に対して HTTP 要求を行う際にそのアクセス トークンが使用されます。 アクセス トークンは要求ヘッダーに追加されます。 

次の図は、一般的な対話型アプリケーションの認証フローを示します。 

![ネイティブ アプリの図](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

上の図の番号は、要求のフローを時系列で表したものです。

> [!NOTE]
> ユーザー認証の方法を使用する場合、すべてのアプリで同じ (既定の) ネイティブ アプリケーション クライアント ID とネイティブ アプリケーション リダイレクト URI が共有されます。 

1. ユーザーに資格情報の入力を求めます。
2. 次のパラメーターが含まれた Azure AD アクセス トークンを要求します。  

   * Azure AD テナント エンドポイント。

       テナント情報は Azure Portal から取得できます。 右上隅のサインインしているユーザーの名前にカーソルを合わせます。
   * Media Services リソース URI。 

       この URI は、同じ Azure 環境内の Media Services アカウントの URI と同じです (例: https:\//rest.media.azure.net)。

   * Media Services (ネイティブ) アプリケーション クライアント ID。
   * Media Services (ネイティブ) アプリケーション リダイレクト URI。
   * REST Media Services のリソース URI。
        
       この URI は REST API エンドポイントを表します (例: https://test03.restv2.westus.media.azure.net/api/) 。

     これらのパラメーターの値を取得するには、[Azure Portal を使用した Azure AD 認証設定へのアクセス](media-services-portal-get-started-with-aad.md)に関するページで、ユーザー認証オプションの使用について参照してください。

3. Azure AD アクセス トークンがクライアントに送信されます。
4. クライアントが、Azure AD アクセス トークンを使用して要求を Azure Media REST API に送信します。
5. Media Services からクライアントにデータが返されます。

Media Services .NET クライアント SDK で Azure AD 認証を使用して REST 要求のやり取りを行う方法については、[.NET で Azure AD 認証を使用して Media Services API にアクセスする方法](media-services-dotnet-get-started-with-aad.md)に関するページを参照してください。 

Media Services .NET クライアント SDK を使用していない場合、手順 2. で説明されているパラメーターを使用して、Azure AD アクセス トークン要求を手動で作成する必要があります。 詳細については、[Azure AD Authentication Library を使用して Azure AD トークンを取得する方法](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)に関するページを参照してください。

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証

この認証方法がよく使用されるアプリケーションは、中間層サービスとスケジュールされたジョブを実行するアプリです (例: Web アプリ、関数アプリ、ロジック アプリ、API、マイクロサービス)。 また、この認証方法は、サービス アカウントを使用してリソースを管理する場合がある対話型アプリケーションにも適しています。

サービス プリンシパル認証方法を使用してコンシューマー向けのシナリオを構築する場合、認証は通常 (何らかの API を介して) 中間層で処理されます。モバイル アプリケーションやデスクトップ アプリケーションで直接処理されることはありません。 

この方法を使用するには、Azure AD アプリケーションとサービス プリンシパルを独自のテナントに作成します。 アプリケーションを作成した後、アプリの共同作成者または所有者ロール アクセス権を Media Services アカウントに付与します。 この操作は、Azure portal、Azure CLI、または PowerShell スクリプトを使用して実行できます。 既存の Azure AD アプリケーションを使用することもできます。 [Azure Portal](media-services-portal-get-started-with-aad.md) で Azure AD アプリとサービス プリンシパルを登録して管理できます。 これは [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) または [PowerShell](media-services-powershell-create-and-configure-aad-app.md) を使用して実行することもできます。 

![中間層アプリ](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Azure AD アプリケーションを作成した後、次の設定の値を取得します。 認証にはこれらの値が必要です。

- クライアント ID 
- クライアント シークレット 

上の図の番号は、要求のフローを時系列で表したものです。
    
1. 中間層アプリ (Web API または Web アプリケーション) が、次のパラメーターが含まれた Azure AD アクセス トークンを要求します。  

   * Azure AD テナント エンドポイント。

       テナント情報は Azure Portal から取得できます。 右上隅のサインインしているユーザーの名前にカーソルを合わせます。
   * Media Services リソース URI。 

       この URI は、同じ Azure 環境内の Media Services アカウントの URI と同じです (例: https:\//rest.media.azure.net)。

   * REST Media Services のリソース URI。

       この URI は REST API エンドポイントを表します (例: https://test03.restv2.westus.media.azure.net/api/) 。

   * Azure AD アプリケーションの値 (クライアント ID と クライアント シークレット)。
    
     これらのパラメーターの値を取得するには、[Azure Portal を使用した Azure AD 認証設定へのアクセス](media-services-portal-get-started-with-aad.md)に関するページで、サービス プリンシパル認証オプションの使用について参照してください。

2. Azure AD アクセス トークンが中間層アプリに送信されます。
4. 中間層アプリが、Azure AD トークンを使用して要求を Azure Media REST API に送信します。
5. Media Services からデータが中間層アプリに返されます。

Media Services .NET クライアント SDK で Azure AD 認証を使用して REST 要求のやり取りを行う方法については、「[Use Azure AD authentication to access Azure Media Services API with .NET (.NET で Azure AD 認証を使用して Azure Media Services API にアクセスする方法)](media-services-dotnet-get-started-with-aad.md)」を参照してください。 

Media Services .NET クライアント SDK を使用していない場合、手順 1. で説明されているパラメーターを使用して、Azure AD トークン要求を手動で作成する必要があります。 詳細については、[Azure AD Authentication Library を使用して Azure AD トークンを取得する方法](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

例外:"リモート サーバーがエラーを返しました: (401) 許可されていません"

解決方法:Media Services REST 要求が成功するには、呼び出し元のユーザーは、アクセスしようとしている Media Services アカウントの共同作成者または所有者ロールである必要があります。 詳細については、「[アクセス制御](media-services-use-aad-auth-to-access-ams-api.md#access-control)」セクションを参照してください。

## <a name="resources"></a>リソース

次の記事では、Azure AD 認証の概念の概要について説明されています。 

- [Azure AD で対応できる認証シナリオ](../../active-directory/develop/authentication-vs-authorization.md)
- [Azure AD でのアプリケーションの追加、更新、または削除](../../active-directory/develop/quickstart-register-app.md)
- [Azure PowerShell を使用して Azure でのロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>次のステップ

* Azure Portal を使って [Azure AD 認証にアクセスし、Azure Media Services API を利用する](media-services-portal-get-started-with-aad.md)。
* [.NET で Azure AD 認証を使用して Azure Media Services API にアクセスする](media-services-dotnet-get-started-with-aad.md)。
