---
title: SQL によって委任された管理者のアクセス許可を使用した Azure AD Connect のインストール | Microsoft Docs
description: このトピックでは、SQL dbo アクセス許可のみを持つアカウントを使用してインストールを許可する Azure AD Connect への更新について説明します。
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f082ec896bf0542b63c8c1d0257679681334050
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85358669"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>SQL によって委任された管理者のアクセス許可を使用した Azure AD Connect のインストール
以前の Azure AD Connect ビルドでは、SQL を必要とする構成をデプロイするとき、管理の委任はサポートされていませんでした。  Azure AD Connect をインストールするユーザーには、SQL サーバーにおけるサーバー管理者 (SA) アクセス許可が必要でした。

Azure AD Connect の最新のリリースでは、SQL 管理者が帯域外でデータベースのプロビジョニングを実行し、データベース所有者権限を持つ Azure AD Connect 管理者がインストールできます。

## <a name="before-you-begin"></a>開始する前に
この機能を使用するには、動的なパーツが複数あり、それぞれに組織内の異なる管理者が関与する可能性があることを認識する必要があります。  次の表に、この機能を使用して Azure AD Connect をデプロイするときの各ロールとそれぞれの役割をまとめます。

|Role|説明|
|-----|-----|
|ドメインまたはフォレスト AD 管理者|同期サービスを実行するときに Azure AD Connect によって使用されるドメイン レベルのサービス アカウントを作成します。  サービス アカウントの詳細については、[アカウントとアクセス許可](reference-connect-accounts-permissions.md)に関するページをご覧ください。
|SQL 管理者|ADSync データベースを作成し、ログインと dbo アクセス権を、Azure AD Connect 管理者、およびドメイン/フォレスト管理者によって作成されたサービス アカウントに付与します。|
Azure AD Connect 管理者|Azure AD Connect をインストールし、カスタム インストール中にサービス アカウントを指定します。

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>SQL によって委任されたアクセス許可を使用して Azure AD Connect をインストールする手順
帯域外でデータベースをプロビジョニングし、データベース所有者アクセス許可で Azure AD Connect をインストールするには、次の手順に従います。

>[!NOTE]
>必須ではありませんが、データベースを作成するときは、Latin1_General_CI_AS 照合順序を選択することを **強くお勧めします**。


1. SQL 管理者に、大文字と小文字を区別しない照合順序 **(Latin1_General_CI_AS)** で ADSync データベースを作成してもらいます。  データベースの名前は **ADSync** にします。  Azure AD Connect のインストール時に、復旧モデル、互換性レベル、およびコンテインメントの種類が正しい値に更新されます。  ただし、照合順序は、SQL 管理者が正しく設定する必要があります。これを行わないと、Azure AD Connect によってインストールがブロックされます。  復旧するには、SA はデータベースを削除して、再作成する必要があります。
 
   ![照合順序](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Azure AD Connect 管理者とドメイン サービス アカウントに次のアクセス許可を付与します。
   - SQL ログイン 
   - **データベース所有者 (dbo)** 権限。
 
   ![アクセス許可](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect は入れ子になったメンバーシップでのログインはサポートしていません。  つまり、Azure AD Connect 管理者アカウントとドメイン サービス アカウントは、dbo 権限が付与されているログインにリンクされている必要があります。  単に dbo 権限によるログインに割り当てられているグループのメンバーとすることはできません。

3. Azure AD Connect のインストール時に使用する必要がある SQL サーバーとインスタンス名を示す電子メールを、Azure AD Connect 管理者に送信します。

## <a name="additional-information"></a>関連情報
データベースがプロビジョニングされたら、Azure AD Connect 管理者は、必要に応じてオンプレミスの同期をインストールおよび構成できます。

SQL 管理者が以前の Azure AD Connect のバックアップから ADSync データベースを復元している場合、既存のデータベースを使用して新しい Azure AD Connect サーバーをインストールする必要があります。 既存のデータベースを使用した Azure AD Connect のインストールの詳細については、「[既存の ADSync データベースを使用して Azure AD Connect をインストールする](how-to-connect-install-existing-database.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
- [簡単設定を使用した Azure AD Connect の開始](how-to-connect-install-express.md)
- [Azure AD Connect のカスタム インストール](how-to-connect-install-custom.md)
- [既存の ADSync データベースを使用して Azure AD Connect をインストールする](how-to-connect-install-existing-database.md)  
