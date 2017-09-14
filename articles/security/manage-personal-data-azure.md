---
title: "Microsoft Azure での個人データの管理 | Microsoft Docs"
description: "Azure Active Directory と Azure SQL Database で個人データを修正、更新、削除、およびエクスポートする方法についてのガイダンス"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3b57c92bd744644ea81878712b4272ed3ece4e2e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="manage-personal-data-in-microsoft-azure"></a>Microsoft Azure での個人データの管理

この記事では、Azure Active Directory と Azure SQL Database で個人データを修正、更新、削除、およびエクスポートする方法について説明します。

## <a name="scenario"></a>シナリオ

ダブリンに拠点を置く会社があり、地域と外国の顧客基盤の両方を対象に、アイルランドや世界中での高級な海外挙式のサービスをワンストップで提供しているとします。 この会社は世界各地にオフィスや顧客、従業員、およびベンダーを抱えており、現地で行き届いたサービスを提供しています。

この会社では、数多くの項目の中から、食品アレルギーや食の嗜好に関する情報が含まれている RSVP (招待状への返信) を記録しています。 結婚式のゲストは乗馬やサーフィン、ボートなどの多彩なアクティビティに申し込めるだけでなく、式までの数か月の間は、中心となる Web ページ上でゲスト同士でやりとりすることもできます。 この会社は従業員やベンダー、顧客や結婚式のゲストから個人情報を収集しています。 国際的に事業を行っているという性質上、複数のレベルからなる規制を遵守する必要があります。

## <a name="problem-statement"></a>問題の説明

- データの管理者は不正確な個人情報を修正し、不完全であったり変更があったりした個人情報を更新できる必要があります。

- データの管理者、データの本人からの要求に応じて、個人情報を削除できる必要があります。

- データの管理者はデータの本人からの要求に応じて、データをエクスポートし一般的かつ構造化された形式で提供する必要があります。

## <a name="company-goals"></a>会社の目標

- 顧客、結婚式のゲスト、従業員およびベンダーの不正確なまたは不完全な個人情報を、Azure Active Directory と Azure SQL Database 上で修正し更新する必要があります。

- Azure Active Directory と Azure SQL Database 上の個人情報は、データの本人からの要求に応じて削除される必要があります。

- 個人情報はデータの本人からの要求に応じて、一般的かつ構造化された形式でエクスポートされる必要があります。

## <a name="solutions"></a>解決方法

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory: 不正確または不完全な個人データの訂正/修正と個人情報/ユーザー プロファイルの消去/削除

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) は、Microsoft が提供するクラウドベースのマルチテナントに対応したディレクトリおよび ID の管理サービスです。
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) 環境では、[Azure Portal](https://portal.azure.com/) を使用して、個人データ (例: ユーザー名、仕事の件名、住所、電話番号) を含む顧客および従業員のユーザー プロファイルとユーザーの勤務先情報を修正、更新、および削除できます。

サインインするときは、ディレクトリの全体管理者であるアカウントを使用する必要があります。

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>Azure Active Directory でユーザー プロファイルや勤務先情報を修正または更新するにはどうすればいいですか

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。

2. **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

    ![メディア/image1.png](media/manage-personal-data-azure/image001.png)

3. **[ユーザーとグループ]** ブレードで、**[ユーザー]** を選択します。

    ![メディア/image2.png](media/manage-personal-data-azure/image003.png)

4. **[ユーザーとグループ - ユーザー]** ブレードで、一覧からユーザーを選択し、選択したユーザーのブレードで **[プロファイル]** を選択して、修正または更新の必要があるユーザー プロファイル情報を表示します。

    ![メディア/image3.png](media/manage-personal-data-azure/image005.png)

5. 情報を修正または更新し、コマンド バーの **[保存]** をクリックします。

6.  選択したユーザーのブレードで **[勤務先情報]** を選択して、修正または更新の必要があるユーザーの勤務先情報を表示します。

    ![メディア/image4.png](media/manage-personal-data-azure/image007.png)

7. 勤務先情報を修正または更新し、コマンド バーの **[保存]** を選択します。

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>Azure Active Directory のユーザー プロファイルを削除するにはどうすればいいですか

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。

2. **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

    ![](media/manage-personal-data-azure/image001.png)

3. **[ユーザーとグループ]** ブレードで、**[ユーザー]** を選択します。

    ![メディア/image2.png](media/manage-personal-data-azure/image003.png)

4. **[ユーザーとグループ - ユーザー]** ブレードで、一覧からユーザーを選択します。

    ![メディア/image3.png](media/manage-personal-data-azure/image007.png)

5. 選択したユーザーのブレードで、**[概要]** を選択し、コマンド バーの **[削除]** をクリックします。

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>SQL Database: 不正確なまたは不完全な個人情報の訂正/修正、個人情報の消去/削除、個人情報のエクスポート 

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) は、開発者がアプリケーションを作成および管理するときに便利なクラウド データベースです。

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) で個人データを更新するには、標準の SQL クエリを使用します。これにより削除も行えます。 また、Azure SQL Server のインポートおよびエクスポート ウィザードなどのさまざまな方法を使用して、SQL Database から個人情報を、さまざまな形式 (例: BACPAC ファイル) でエクスポートできます。

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>SQL Database で個人情報を修正、更新、消去するにはどうすればいいですか

SQL Database で個人情報を修正または更新する方法については、「[Update (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql)」、[Update Text による更新](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql)、[共通テーブル式を使用した更新](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql)、または [Write Text による更新](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql)に関するドキュメントをご覧ください。

SQL Database で個人情報を削除する方法については、「[Delete (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql)」のドキュメントをご覧ください。

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>SQL Database の BACPAC ファイルに個人情報をエクスポートするにはどうすればいいですか

BACPAC ファイルは、SQL Database のデータとメタデータが含まれる、拡張子に BACPAC が付く ZIP ファイルです。 エクスポートは、[Azure Portal](https://portal.azure.com/)、SQLPackage コマンド ライン ユーティリティ、SQL Server Management Studio (SSMS)、または PowerShell を使用して行えます。

BACPAC ファイルにデータをエクスポートする方法については、「[Azure SQL Database を BACPAC ファイルにエクスポートする](https://docs.microsoft.com/azure/sql-database/sql-database-export)」のページをご覧ください。このページで上記の各方法の詳細な手順が説明されています。

SQL Server インポートおよびエクスポート ウィザードを使用して SQL Database から個人情報をエクスポートするにはどうすればいいですか

このウィザードを使用すると、データをソースから保存先にコピーできます。 ウィザードの入手方法、アクセス許可に関する情報、およびツールに関するヘルプなどウィザードの概要については、「[SQL Server インポートおよびエクスポート ウィザードを使用してデータをインポートおよびエクスポートする](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard)」の Web ページをご覧ください。

ウィザードの手順の概要については、「[Steps in the SQL Server Import and Export Wizard (SQL Server インポートおよびエクスポート ウィザードの手順)](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard)」の Web ページをご覧ください。

## <a name="next-steps"></a>次のステップ:

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)


