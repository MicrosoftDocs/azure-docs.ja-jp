---
title: Azure ポータルで Azure AD 認証を開始する | Microsoft Docs
description: Azure ポータルを使用して Azure Active Directory (Azure AD) 認証にアクセスして Azure Media Services API を利用する方法を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 37c5a97d5f7a872e916f223f2cdfc98bcdec3cad
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258430"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Azure ポータルで Azure AD 認証を開始する

Azure ポータルを使用して Azure Active Directory (Azure AD) 認証にアクセスして Azure Media Services API にアクセスする方法を説明します。

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 アカウントをお持ちでない場合は、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)で作業を開始してください。 
- Media Services アカウント。 詳細については、「[Azure ポータルを使用した Azure Media Services アカウントの作成](media-services-portal-create-account.md)」を参照してください。
- [Azure AD 認証による Azure Media Services API へのアクセスの概要](media-services-use-aad-auth-to-access-ams-api.md)に関するトピックを見直していること。 

Azure Media Services で Azure AD 認証を使用する場合、次の 2 つの認証オプションがあります。

- **ユーザー認証**。 Media Services リソースを操作するアプリを使用しているユーザーが認証を受けます。 ユーザーは最初に対話型アプリケーションから資格情報の入力を求められます。 例として、承認済みユーザーがエンコード ジョブまたはライブ ストリーミングを監視するために使用する管理コンソール アプリがあります。 
- **サービス プリンシパル認証**。 サービスが認証を受けます。 この認証方法がよく使用されるアプリケーションは、デーモン サービス、中間層サービス、またはスケジュールされたジョブを実行するアプリ (Web アプリ、関数アプリ、ロジック アプリ、API、マイクロサービス) です。

> [!IMPORTANT]
> 現在 Media Services では、Azure Access Control Service 認証モデルがサポートされています。 ただし、Access Control 承認は 2018 年 6 月 1 日に非推奨となる予定です。 できるだけ早く Azure AD 認証モデルに移行することをお勧めします。

## <a name="select-the-authentication-method"></a>認証方法を選択する

1. [Azure ポータル](https://portal.azure.com/) で Media Services アカウントを選択します。
2. Media Services API に接続する方法を選択します。

    ![接続方法選択ページ](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>ユーザー認証

ユーザー認証オプションを指定して Media Services API に接続するには、クライアント アプリから、以下のパラメーターを使用して、Azure AD トークンを要求する必要があります。  

* Azure AD テナント エンドポイント
* Media Services リソース URI
* Media Services (ネイティブ) アプリケーション クライアント ID 
* Media Services (ネイティブ) アプリケーション リダイレクト URI 
* REST Media Services のリソース URI

これらのパラメーターの値は、**[ユーザー認証を使って Media Services API に接続する]** ページで取得することができます。 

![[ユーザー認証を使って Media Services API に接続する] ページ](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Media Services Microsoft .NET SDK を使用して Media Services API に接続する場合、必要な値は SDK の一部として入手できます。 詳細については、「[.NET で Azure AD 認証を使用して Azure Media Services API にアクセスする](media-services-dotnet-get-started-with-aad.md)」を参照してください。

Media Services .NET クライアント SDK を使用しない場合は、前述のパラメーターを使用して、Azure AD トークン要求を手動で作成する必要があります。 詳細については、[Azure AD Authentication Library を使用して Azure AD トークンを取得する方法](../../active-directory/develop/active-directory-authentication-libraries.md)に関するページを参照してください。

## <a name="service-principal-authentication"></a>サービス プリンシパル認証

サービス プリンシパル オプションを指定して Media Services API に接続するには、中間層アプリ (Web API または Web アプリケーション) から、以下のパラメーターを使用して Azure AD トークンを要求する必要があります。  

* Azure AD テナント エンドポイント
* Media Services リソース URI 
* REST Media Services のリソース URI
* Azure AD アプリケーションの値: **クライアント ID** と**クライアント シークレット**

これらのパラメーターの値は、**[サービス プリンシパルを使って Media Services API に接続する]** ページで取得することができます。 このページを使用して、新しい Azure AD アプリケーションを作成するか、既存の Azure AD アプリケーションを選択します。 Azure AD アプリを選択すると、クライアント ID (アプリケーション ID) を取得し、クライアント シークレット (キー) の値を生成できます。 

![[サービス プリンシパルを使って Media Services API に接続する] ページ](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

次の条件を満たす最初の Azure AD アプリケーションが選択された状態で、**[サービス プリンシパル]** ブレードが開きます。

- 登録済みの Azure AD アプリケーションであること。
- アカウントに共同作成者または所有者ロールベースのアクセス制御許可があること。

Azure AD アプリを作成または選択した後、クライアント シークレット (キー) とクライアント ID (アプリケーション ID) を作成してコピーできます。 このシナリオでは、アクセス トークンを取得するためにクライアント ID とクライアント シークレットが必要です。

ドメイン内に Azure AD アプリを作成するアクセス許可を持っていない場合、ブレードの Azure AD アプリ コントロールは表示されず、警告メッセージが表示されます。

Media Services .NET SDK を使用して Media Services API に接続する場合は、「[.NET で Azure AD 認証を使用して Azure Media Services API にアクセスする](media-services-dotnet-get-started-with-aad.md)」を参照してください。

Media Services .NET クライアント SDK を使用しない場合は、前述のパラメーターを使用して、Azure AD トークン要求を手動で作成する必要があります。 詳細については、[Azure AD Authentication Library を使用して Azure AD トークンを取得する方法](../../active-directory/develop/active-directory-authentication-libraries.md)に関するページを参照してください。

### <a name="get-the-client-id-and-client-secret"></a>クライアント ID とクライアント シークレットを取得する

既存の Azure AD アプリを選択するか新規に作成するオプションを選択すると、次のボタンが表示されます。

![[アクセス許可の管理] ボタンと [アプリケーションの管理] ボタン](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

[Azure AD アプリケーション] ブレードを開くには、**[アプリケーションの管理]** をクリックします。 **[アプリケーションの管理]** ブレードで、アプリのクライアント ID (アプリケーション ID) を取得できます。 クライアント シークレット (キー) を生成するには、**[キー]** を選択します。

![[アプリケーションの管理] ブレードの [キー] オプション](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>アクセス許可とアプリケーションを管理する

Azure AD アプリケーションを選択して、アプリケーションとアクセス許可を管理できます。 その他のアプリケーションにアクセスできるように Azure AD アプリケーションをセットアップするには、**[アクセス許可の管理]** をクリックします。 キーと応答 URL の変更やアプリケーションのマニフェストの編集などの管理タスクを行うには、**[アプリケーションの管理]** をクリックします。

### <a name="edit-the-apps-settings-or-manifest"></a>アプリの設定またはマニフェストを編集する

アプリの設定またはマニフェストを編集するには、**[アプリケーションの管理]** をクリックします。

![[アプリケーションの管理] ページ](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>次の手順

[アカウントへのファイルのアップロード](media-services-portal-upload-files.md)を開始します。
