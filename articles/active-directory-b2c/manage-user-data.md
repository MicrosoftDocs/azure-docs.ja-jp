---
title: Azure Active Directory B2C でのユーザー データの管理 | Microsoft Docs
description: Azure AD B2C 内のユーザー データを削除またはエクスポートする方法を説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 4cbca467b50dd0e43132b6d09dc0785c501fca0f
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204690"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でのユーザー データの管理

 この記事では、[Azure Active Directory Graph API](/previous-versions/azure/ad/graph/api/api-catalog) によって提供される操作を使用して、Azure Active Directory (Azure AD) B2C 内のユーザー データを管理する方法について説明します。 ユーザー データの管理には、監査ログからのデータの削除またはエクスポートがあります。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>ユーザー データを削除する

ユーザー データは、Azure AD B2C ディレクトリおよび監査ログに格納されます。 すべてのユーザー監査データは、Azure AD B2C に 7 日間保持されます。 その 7 日の期間内にユーザー データを削除する場合は、[ユーザーの削除](/previous-versions/azure/ad/graph/api/users-operations#DeleteUser)操作を使用できます。 データが存在する Azure AD B2C テナントごとに、DELETE 操作を行う必要があります。 

Azure AD B2C 内のすべてのユーザーには、オブジェクト ID が割り当てられています。 オブジェクト ID は、Azure AD B2C 内のユーザー データの削除に使用する明確な識別子を提供します。 アーキテクチャによっては、オブジェクト ID は、他のサービス (財務データベース、マーケティング データベース、顧客関係管理データベースなど) との間の便利な相関関係識別子になる可能性があります。 

ユーザーのオブジェクト ID を取得する最も確実な方法は、Azure AD B2C での認証過程の一部として入手することです。 他の方法を使ってユーザーからデータの有効な要求を受け取った場合、顧客サービス サポート エージェントによる検索などのオフライン プロセスでは、ユーザーを検索して、関連付けられているオブジェクト ID を記録することが必要な場合があります。 

次の例では、可能なデータ削除フローを示します。

1. ユーザーがサインインし、 **[自分のデータを削除]** を選びます。
2. アプリケーションは、アプリケーションの管理セクション内のデータを削除するオプションを提供します。
3. アプリケーションは、Azure AD B2C に対する認証を強制します。 Azure AD B2C は、ユーザーのオブジェクト ID を含むトークンをアプリケーションに提供します。 
4. アプリケーションによってトークンが受け取られます。Azure AD Graph API への呼び出しを通じてユーザー データを削除するために、オブジェクト ID が使用されます。 Azure AD Graph API によってユーザー データが削除され、状態コード 200 OK が返されます。
5. 必要に応じて、アプリケーションによってオブジェクト ID または他の識別子が使用され、他の組織システムに含まれているユーザー データの削除が調整されます。
6. アプリケーションは、データの削除を確認し、ユーザーに次の手順を提供します。

## <a name="export-customer-data"></a>顧客データをエクスポートする

Azure AD B2C から顧客データをエクスポートするプロセスは、削除のプロセスと似ています。

Azure AD B2C のユーザー データは以下に限定されます。

- **Azure Active Directory に格納されたデータ**:オブジェクト ID または任意のサインイン名 (メール アドレスやユーザー名など) を使用して、Azure AD B2C の認証ユーザー体験でデータを取得できます。 
- **ユーザー固有の監査イベント レポート**:オブジェクト ID を使用して、データにインデックスを付けることができます。

エクスポート データ フローの次の例において、アプリケーションによって実行されると説明されている手順は、ディレクトリの管理者ロールを持つユーザーまたはバックエンド プロセスが実行することもできます。

1. ユーザーがアプリケーションにサインインします。 必要な場合は、Azure Multi-Factor Authentication による認証が Azure AD B2C によって強制されます。
2. ユーザー属性を取得する Azure AD Graph API 操作を呼び出すために、アプリケーションによってユーザーの資格情報が使用されます。 Azure AD Graph API は、JSON 形式で属性データを提供します。 スキーマによっては、ユーザーに関するすべての個人データが含まれるよう、ID トークンの内容を設定できます。
3. アプリケーションによってユーザーの監査アクティビティが取得されます。 Azure AD Graph API によってイベント データがアプリケーションに提供されます。
4. アプリケーションによってデータが集計され、ユーザーがそのデータを使用できるようになります。

## <a name="next-steps"></a>次の手順

- ユーザーによるアプリケーションへのアクセスを管理する方法については、[ユーザー アクセスの管理](manage-user-access.md)に関するページを参照してください。




