---
title: Azure Analysis Services の認証とユーザーのアクセス許可 | Microsoft Docs
description: この記事では、Azure Analysis Services で、ID 管理とユーザー認証に Azure Active Directory (Azure AD) が使用されるしくみについて説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 551bae56565140da3754e74a23b1cc18087f1171
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96487441"
---
# <a name="authentication-and-user-permissions"></a>認証とユーザーのアクセス許可

Azure Analysis Services では、ID 管理とユーザー認証に Azure Active Directory (Azure AD) を使用します。 Azure Analysis Services サーバーを作成、管理、またはこのサーバーに接続するユーザーには、同じサブスクリプション内の [Azure AD テナント](../active-directory/fundamentals/active-directory-whatis.md)に有効なユーザー ID が必要です。

Azure Analysis Services では、[Azure AD B2B コラボレーション](../active-directory/external-identities/what-is-b2b.md)がサポートされます。 B2B では、組織外のユーザーを Azure AD ディレクトリにゲスト ユーザーとして招待することができます。 別の Azure AD テナント ディレクトリのユーザーまたは任意の有効なメール アドレスを持つユーザーをゲストにすることができます。 招待されたユーザーが、Azure から電子メールで送信される招待状を受け入れると、ユーザー ID がテナント ディレクトリに追加されます。 それらの ID をセキュリティ グループに、またはサーバー管理者またはデータベース ロールのメンバーとして追加できます。

![Azure Analysis Services 認証のアーキテクチャ](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>認証

すべてのクライアント アプリケーションとツールは、Analysis Services [クライアント ライブラリ](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (AMO、MSOLAP、ADOMD) の 1 つ以上を使ってサーバーに接続します。 

3 つのクライアント ライブラリはすべて、Azure AD の対話型フローと非対話型認証方法の両方をサポートします。 2 つの非対話型方法である Active Directory パスワード認証方法と Active Directory 統合認証方法は、AMOMD と MSOLAP を利用しているアプリケーションで使用できます。 これら 2 つの方式では、ポップアップ ダイアログ ボックスは表示されません。

Excel や Power BI Desktop などのクライアント アプリケーションと、SSMS や Visual Studio 向け Analysis Services プロジェクト拡張機能などのツールは、最新リリースに更新されたときにライブラリの最新バージョンをインストールします。 Power BI Desktop、SSMS、Analysis Services プロジェクト拡張機能は毎月更新されます。 Excel は [Microsoft 365 で更新されています](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516)。 Microsoft 365 の更新は頻度が低く、組織によっては遅延チャネルを使用して、更新が最大 3 か月間遅延されるようにしています。

使用するクライアント アプリケーションまたはツールに応じて、認証の種類とサインインの方法が異なる場合があります。 各アプリケーションは、Azure Analysis Services のようなクラウド サービスに接続するためのさまざまな機能をサポートしている場合があります。

Power BI Desktop、Visual Studio、SSMS では、Azure AD Multi-Factor Authentication (MFA) もサポートされている対話型の認証方式である Active Directory のユニバーサル認証がサポートされています。 Azure AD MFA は、シンプルなサインイン プロセスを提供しながら、データやアプリケーションへのアクセスを効果的に保護することができます。 Azure MFA は、複数の検証オプション (電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、モバイル アプリ通知) による強力な認証を提供します。 Azure AD との対話型 MFA はポップアップ ダイアログ ボックスで検証できます。 **ユニバーサル認証を使うことをお勧めします**。

ユニバーサル認証が選択されていないか、使用できない (Excel) 場合に、Windows アカウントを使って Azure にサインインするには、[Active Directory フェデレーション サービス (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) が必要です。 フェデレーションでは、Azure AD および Microsoft 365 のユーザーはオンプレミスの資格情報を使って認証されて、Azure リソースにアクセスできます。

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Azure Analysis Services サーバーは、Windows 認証、Active Directory パスワード認証、Active Directory のユニバーサル認証を使って [SSMS V17.1](/sql/ssms/download-sql-server-management-studio-ssms) 以降からの接続をサポートします。 一般に、以下の理由で Active Directory のユニバーサル認証の使用をお勧めします。

*  対話型と非対話型の認証方法をサポートしています。

*  Azure AS テナントに招待された Azure B2B ゲスト ユーザーをサポートしています。 サーバーへの接続時に、ゲスト ユーザーは Active Directory のユニバーサル認証を選ぶ必要があります。

*  Multi-Factor Authentication (MFA) をサポートします。 Azure AD MFA は、電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、モバイル アプリ通知など、各種確認オプションでデータとアプリケーションへのアクセスを保護するのに役立ちます。 Azure AD との対話型 MFA はポップアップ ダイアログ ボックスで検証できます。

### <a name="visual-studio"></a>Visual Studio

Visual Studio は、MFA サポートがある Active Directory のユニバーサル認証を使って Azure Analysis Services に接続します。 ユーザーは、最初のデプロイ時に Azure にサインインするよう求められます。 ユーザーは、デプロイしているサーバーでサーバー管理者のアクセス許可を持つアカウントを使って Azure にサインインする必要があります。 初めて Azure にサインインするときに、トークンが割り当てられます。 将来の再接続のためにトークンはメモリにキャッシュされます。

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop は、MFA サポートがある Active Directory のユニバーサル認証を使って Azure Analysis Services に接続します。 ユーザーは、最初の接続時に Azure にサインインするよう求められます。 ユーザーは、サーバー管理者またはデータベース ロールに含まれているアカウントを使用して Azure にサインインする必要があります。

### <a name="excel"></a>Excel

Excel ユーザーは、Windows アカウント、組織 ID (メール アドレス)、または外部のメール アドレスを使ってサーバーに接続できます。 外部の電子メール ID は、ゲスト ユーザーとして Azure AD に存在する必要があります。

## <a name="user-permissions"></a>ユーザーのアクセス許可

**サーバー管理者** は、Azure Analysis Services サーバー インスタンスに固有です。 Azure Portal、SSMS、Visual Studio などのツールを使って接続し、データベースの追加やユーザー ロールの管理などのタスクを実行します。 既定では、サーバーを作成したユーザーは、自動的に Analysis Services サーバー管理者として追加されます。 他の管理者は、Azure Portal または SSMS を使って追加できます。 サーバー管理者には、同じサブスクリプションの Azure AD テナントにアカウントが必要です。 詳しくは、「[サーバー管理者の管理](analysis-services-server-admins.md)」をご覧ください。 

**データベース ユーザー** は、Excel や Power BI などのクライアント アプリケーションを使ってモデル データベースに接続します。 ユーザーは、データベース ロールに追加する必要があります。 データベース ロールは、データベースに対して管理者、プロセス、または読み取りアクセス許可を定義します。 管理者のアクセス許可を含むロールのデータベース ユーザーは、サーバー管理者とは異なることを理解することが重要です。 ただし、既定では、サーバー管理者はデータベース管理者でもあります。 詳しくは、「[データベース ロールとユーザーの管理](analysis-services-database-users.md)」をご覧ください。

**Azure リソース所有者**。 リソース所有者は、Azure サブスクリプションのリソースを管理します。 リソース所有者は、Azure Portal の **[アクセス制御]** を使用して、または Azure Resource Manager テンプレートで、サブスクリプション内の所有者または共同作成者のロールに Azure AD ユーザー ID を追加できます。 

![Azure Portal の [アクセス制御 (IAM)]](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

このレベルのロールは、Portal で完了可能なタスクまたは Azure Resource Manager テンプレートを使って完了可能なタスクを実行する必要のあるユーザーまたはアカウントに適用します。 詳細については、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) に関する記事を参照してください。 

## <a name="database-roles"></a>データベース ロール

 テーブル モデル向けに定義されているロールは、データベース ロールです。 つまり、ロールには、Azure AD ユーザーとセキュリティ グループから構成されるメンバーが含まれます。このセキュリティ グループは、メンバーが model データベースに対して実行できるアクションを定義する特定のアクセス許可を持ちます。 データベース ロールは、データベース内に個別のオブジェクトとして作成され、そのロールが作成されたデータベースにのみ適用されます。   
  
 既定では、テーブル モデル プロジェクトの新規作成時点では、モデル プロジェクトにロールはありません。 ロールは、Visual Studio の [ロール マネージャー] ダイアログ ボックスを使用して定義できます。 モデル プロジェクトのデザイン時にロールが定義された場合、それらはモデル ワークスペース データベースにのみ適用されます。 モデルを配置すると、同じロールが配置済みモデルに適用されます。 モデルのデプロイ後、サーバーとデータベースの管理者は、SSMS を使ってロールとメンバーを管理できます。 詳しくは、「[データベース ロールとユーザーの管理](analysis-services-database-users.md)」をご覧ください。
  
## <a name="next-steps"></a>次のステップ

[Azure Active Directory のグループによるリソースへのアクセス管理](../active-directory/fundamentals/active-directory-manage-groups.md)   
[データベース ロールとユーザーの管理](analysis-services-database-users.md)  
[サーバー管理者の管理](analysis-services-server-admins.md)  
[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md)