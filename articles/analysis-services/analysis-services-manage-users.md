---
title: Azure Analysis Services での認証とユーザーのアクセス許可 | Microsoft Docs
description: Azure Analysis Services での認証とユーザーのアクセス許可について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7673b115a3ad2e6ca7aec34b1cfabfb38d2a16f4
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191362"
---
# <a name="authentication-and-user-permissions"></a>認証とユーザーのアクセス許可

Azure Analysis Services では、ID 管理とユーザー認証に Azure Active Directory (Azure AD) を使用します。 Azure Analysis Services サーバーを作成、管理、またはこのサーバーに接続するユーザーには、同じサブスクリプション内の [Azure AD テナント](../active-directory/fundamentals/active-directory-administer.md)に有効なユーザー ID が必要です。

Azure Analysis Services では、[Azure AD B2B コラボレーション](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)がサポートされます。 B2B では、組織外のユーザーを Azure AD ディレクトリにゲスト ユーザーとして招待することができます。 別の Azure AD テナント ディレクトリのユーザーまたは任意の有効なメール アドレスを持つユーザーをゲストにすることができます。 招待されたユーザーが、Azure から電子メールで送信される招待状を受け入れると、ユーザー ID がテナント ディレクトリに追加されます。 それらの ID をセキュリティ グループに、またはサーバー管理者またはデータベース ロールのメンバーとして追加できます。

![Azure Analysis Services 認証のアーキテクチャ](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Authentication

すべてのクライアント アプリケーションとツールは、Analysis Services [クライアント ライブラリ](analysis-services-data-providers.md) (AMO、MSOLAP、ADOMD) の 1 つ以上を使ってサーバーに接続します。 

3 つのクライアント ライブラリはすべて、Azure AD の対話型フローと非対話型認証方法の両方をサポートします。 2 つの非対話型方法である Active Directory パスワード認証方法と Active Directory 統合認証方法は、AMOMD と MSOLAP を利用しているアプリケーションで使用できます。 これら 2 つの方式では、ポップアップ ダイアログ ボックスは表示されません。

Excel や Power BI Desktop などのクライアント アプリケーションと、SSMS や SSDT などのツールは、最新リリースに更新されたときにライブラリの最新バージョンをインストールします。 Power BI Desktop、SSMS、SSDT は毎月更新されます。 Excel は [Office 365 と共に更新](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516)されます。 Office 365 の更新は頻度が低く、組織によっては遅延チャネルを使用して、更新が最大 3 か月間遅延されるようにしています。

使用するクライアント アプリケーションまたはツールに応じて、認証の種類とサインインの方法が異なる場合があります。 各アプリケーションは、Azure Analysis Services のようなクラウド サービスに接続するためのさまざまな機能をサポートしている場合があります。

Power BI Desktop、SSDT、および SSMS は、Azure Multi-Factor Authentication (MFA) もサポートする対話型の認証方式である Active Directory ユニバーサル認証をサポートします。 Azure MFA は、シンプルなサインイン プロセスを提供しながら、データやアプリケーションへのアクセスを効果的に保護することができます。 Azure MFA は、複数の検証オプション (電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、モバイル アプリ通知) による強力な認証を提供します。 Azure AD との対話型 MFA はポップアップ ダイアログ ボックスで検証できます。 **ユニバーサル認証を使うことをお勧めします**。

ユニバーサル認証が選択されていないか、使用できない (Excel) 場合に、Windows アカウントを使って Azure にサインインするには、[Active Directory フェデレーション サービス (AD FS)](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) が必要です。 フェデレーションでは、Azure AD および Office 365 のユーザーはオンプレミスの資格情報を使って認証されて、Azure リソースにアクセスできます。

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Azure Analysis Services サーバーは、Windows 認証、Active Directory パスワード認証、Active Directory のユニバーサル認証を使って [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 以降からの接続をサポートします。 一般に、以下の理由で Active Directory のユニバーサル認証の使用をお勧めします。

*  対話型と非対話型の認証方法をサポートしています。

*  Azure AS テナントに招待された Azure B2B ゲスト ユーザーをサポートしています。 サーバーへの接続時に、ゲスト ユーザーは Active Directory のユニバーサル認証を選ぶ必要があります。

*  Multi-Factor Authentication (MFA) をサポートします。 Azure MFA は、電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、モバイル アプリ通知など、各種確認オプションでデータとアプリケーションへのアクセスを保護するのに役立ちます。 Azure AD との対話型 MFA はポップアップ ダイアログ ボックスで検証できます。

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)

SSDT は、MFA サポートがある Active Directory のユニバーサル認証を使って Azure Analysis Services に接続します。 ユーザーは、最初のデプロイ時に Azure にサインインするよう求められます。 ユーザーは、デプロイしているサーバーでサーバー管理者のアクセス許可を持つアカウントを使って Azure にサインインする必要があります。 初めて Azure にサインインするときに、トークンが割り当てられます。 SSDT は、将来の再接続のためにトークンをメモリにキャッシュします。

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop は、MFA サポートがある Active Directory のユニバーサル認証を使って Azure Analysis Services に接続します。 ユーザーは、最初の接続時に Azure にサインインするよう求められます。 ユーザーは、サーバー管理者またはデータベース ロールに含まれているアカウントを使用して Azure にサインインする必要があります。

### <a name="excel"></a>Excel

Excel ユーザーは、Windows アカウント、組織 ID (メール アドレス)、または外部のメール アドレスを使ってサーバーに接続できます。 外部の電子メール ID は、ゲスト ユーザーとして Azure AD に存在する必要があります。

## <a name="user-permissions"></a>ユーザーのアクセス許可

**サーバー管理者**は、Azure Analysis Services サーバー インスタンスに固有です。 Azure Portal、SSMS、SSDT などのツールを使って接続し、データベースの追加やユーザー ロールの管理などのタスクを実行します。 既定では、サーバーを作成したユーザーは、自動的に Analysis Services サーバー管理者として追加されます。 他の管理者は、Azure Portal または SSMS を使って追加できます。 サーバー管理者には、同じサブスクリプションの Azure AD テナントにアカウントが必要です。 詳しくは、「[サーバー管理者の管理](analysis-services-server-admins.md)」をご覧ください。 

**データベース ユーザー**は、Excel や Power BI などのクライアント アプリケーションを使ってモデル データベースに接続します。 ユーザーは、データベース ロールに追加する必要があります。 データベース ロールは、データベースに対して管理者、プロセス、または読み取りアクセス許可を定義します。 管理者のアクセス許可を含むロールのデータベース ユーザーは、サーバー管理者とは異なることを理解することが重要です。 ただし、既定では、サーバー管理者はデータベース管理者でもあります。 詳しくは、「[データベース ロールとユーザーの管理](analysis-services-database-users.md)」をご覧ください。

**Azure リソース所有者**。 リソース所有者は、Azure サブスクリプションのリソースを管理します。 リソース所有者は、Azure Portal の **[アクセス制御]** を使用して、または Azure Resource Manager テンプレートで、サブスクリプション内の所有者または共同作成者のロールに Azure AD ユーザー ID を追加できます。 

![Azure Portal の [アクセス制御 (IAM)]](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

このレベルのロールは、Portal で完了可能なタスクまたは Azure Resource Manager テンプレートを使って完了可能なタスクを実行する必要のあるユーザーまたはアカウントに適用します。 詳しくは、「[ロールベースのアクセス制御](../role-based-access-control/overview.md)」をご覧ください。 

## <a name="database-roles"></a>データベース ロール

 表形式モデルに定義されたロールはデータベース ロールです。 つまり、ロールには、Azure AD ユーザーとセキュリティ グループから構成されるメンバーが含まれます。このセキュリティ グループは、メンバーが model データベースに対して実行できるアクションを定義する特定のアクセス許可を持ちます。 データベース ロールは、データベース内に個別のオブジェクトとして作成され、そのロールが作成されたデータベースにのみ適用されます。   
  
 既定では、新しい表形式モデル プロジェクトを作成するときに、モデル プロジェクトはロールを持ちません。 ロールは、SSDT の [ロール マネージャー] ダイアログ ボックスを使用して定義できます。 モデル プロジェクトのデザイン時にロールが定義された場合、それらはモデル ワークスペース データベースにのみ適用されます。 モデルをデプロイすると、デプロイされたモデルに同じロールが適用されます。 モデルのデプロイ後、サーバーとデータベースの管理者は、SSMS を使ってロールとメンバーを管理できます。 詳しくは、「[データベース ロールとユーザーの管理](analysis-services-database-users.md)」をご覧ください。
  
## <a name="next-steps"></a>次の手順

[Azure Active Directory のグループによるリソースへのアクセス管理](../active-directory/fundamentals/active-directory-manage-groups.md)   
[データベース ロールとユーザーの管理](analysis-services-database-users.md)  
[サーバー管理者の管理](analysis-services-server-admins.md)  
[ロールベースのアクセス制御](../role-based-access-control/overview.md)  