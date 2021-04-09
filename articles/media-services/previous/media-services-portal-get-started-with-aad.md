---
title: Azure Portal で Azure AD 認証を開始する | Microsoft Docs
description: Azure Portal を使用して Azure Active Directory (Azure AD) 認証にアクセスして Azure Media Services API を利用する方法を説明します。
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
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d9ad439fe3f41d9e2634fbf9a76cfd21114a5dbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013059"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Azure Portal で Azure AD 認証を開始する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

Azure Portal を使用して Azure Active Directory (Azure AD) 認証にアクセスして Azure Media Services API にアクセスする方法を説明します。

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 アカウントをお持ちでない場合は、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)で作業を開始してください。 
- Media Services アカウント。 詳細については、「[Azure Portal を使用した Azure Media Services アカウントの作成](media-services-portal-create-account.md)」を参照してください。

Azure Media Services で Azure AD 認証を使用する場合、次の 2 つの認証オプションがあります。

- **サービス プリンシパル認証**。 サービスが認証を受けます。 この認証方法がよく使用されるアプリケーションは、デーモン サービス、中間層サービス、またはスケジュールされたジョブを実行するアプリ (Web アプリ、関数アプリ、ロジック アプリ、API、マイクロサービス) です。
- **ユーザー認証**。 Media Services リソースを操作するアプリを使用しているユーザーが認証を受けます。 ユーザーは最初にその操作アプリケーションから資格情報の入力を求められます。 例として、承認済みユーザーがエンコード ジョブまたはライブ ストリーミングを監視するために使用する管理コンソール アプリがあります。 

## <a name="access-the-media-services-api"></a>Media Services API にアクセスする

このページでは、API への接続に使用する認証方法を選択できます。 このページには、API に接続するために必要な値も表示されます。

1. [Azure Portal](https://portal.azure.com/) で Media Services アカウントを選択します。
2. Media Services API に接続する方法を選択します。
3. **[Media Services API に接続する]** で、接続する Media Services API のバージョンを選択します。

## <a name="service-principal-authentication--recommended"></a>サービス プリンシパル認証 (推奨)

Azure Active Directory (Azure AD) アプリとシークレットを使用してサービスを認証します。 これは、Media Services API を呼び出す中間層サービスに対して推奨されます。 たとえば、Web Apps、Functions、Logic Apps、API、マイクロサービスなどです。 これは、推奨されている認証方法です。

### <a name="manage-your-azure-ad-app-and-secret"></a>Azure AD アプリとシークレットの管理

**[AAD アプリとシークレットの管理]** セクションでは、新しい Azure AD アプリを選択または作成し、シークレットを生成できます。 セキュリティ上の理由により、ブレードを閉じた後にシークレットを表示することはできません。 アプリケーションでは、認証にアプリケーション ID とシークレットを使用して、メディア サービスの有効なトークンを取得します。

Azure AD テナントにアプリケーションを登録し、アプリケーションを Azure サブスクリプションのロールに割り当てるための十分なアクセス許可があることを確認してください。 詳細については、「[必要なアクセス許可](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)」を参照してください。

### <a name="connect-to-media-services-api"></a>Media Services API に接続する

**[Media Services API に接続する]** では、サービス プリンシパル アプリケーションへの接続に使用する値が提供されます。 テキスト値を取得したり、JSON または XML ブロックをコピーしたりできます。

## <a name="user-authentication"></a>ユーザー認証

このオプションは、アプリを使用して Media Services リソースを操作している Azure Active Directory の従業員またはメンバーを認証するために使用できます。 ユーザーは最初に、対話型アプリケーションからユーザー資格情報の入力を求められます。 この認証方法は、管理アプリケーションに対してのみ使用してください。

### <a name="connect-to-media-services-api"></a>Media Services API に接続する

**[Media Services API に接続する]** セクションから、ユーザー アプリケーションに接続するための資格情報をコピーします。 テキスト値を取得したり、JSON または XML ブロックをコピーしたりできます。

## <a name="next-steps"></a>次のステップ

[アカウントへのファイルのアップロード](media-services-portal-upload-files.md)を開始します。
