---
title: "Generic SQL コネクタのステップ バイ ステップ | Microsoft Docs"
description: "この記事では、Generic SQL コネクタを利用し、簡単な HR システムを段階的に説明します。"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 28c1cc60-24fd-4d0d-a36d-b4aba6de86e7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7db56a4c0efb208591bb15aa03a4c0dbf833d22e
ms.openlocfilehash: a17bf35abb54eded78d112ac4b25ddebaf5fb5e8


---
# <a name="generic-sql-connector-step-by-step"></a>Generic SQL コネクタのステップ バイ ステップ
このトピックはステップ バイ ステップ ガイドです。 簡単なサンプル HR データベースを作成し、それを利用してユーザーとそのグループ メンバーシップをインポートします。

## <a name="prepare-the-sample-database"></a>サンプル データベースの準備
SQL Server を実行しているサーバーで、[付録 A](#appendix-a) にある SQL スクリプトを実行します。このスクリプトによって、GSQLDEMO という名前のサンプル データベースが作成されます。 作成されたデータベースのオブジェクト モデルは次の図のようになります。  
![オブジェクト モデル](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/objectmodel.png)

また、データベースに接続する際に利用するユーザーを作成します。 このチュートリアルでは、ユーザーは FABRIKAM\SQLUser という名前であり、ドメインに置かれています。

## <a name="create-the-odbc-connection-file"></a>ODBC 接続ファイルの作成
Generic SQL コネクタは ODBC を使用してリモート サーバーに接続します。 最初に、ODBC 接続情報を含むファイルを作成する必要があります。

1. サーバーで ODBC 管理ユーティリティを起動します。  
   ![ODBC](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc.png)
2. **[ファイル DSN]**タブを選択します。 **[追加]**をクリックします。  
   ![ODBC1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc1.png)
3. 既定のドライバーで問題ありません。それを選択し、**[次へ >]** をクリックしてください。  
   ![ODBC2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc2.png)
4. ファイルに名前を付けます。たとえば、**GenericSQL** にします。  
   ![ODBC3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc3.png)
5. **[完了]**をクリックします。  
   ![ODBC4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc4.png)
6. 次に接続を構成します。 データ ソースに適切な説明を加え、SQL Server を実行しているサーバーの名前を指定します。  
   ![ODBC5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc5.png)
7. SQL による認証方法を選択します。 ここでは Windows 認証を使用します。  
   ![ODBC6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc6.png)
8. サンプル データベースの名前として、「 **GSQLDEMO**」と入力します。  
   ![ODBC7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc7.png)
9. この画面ではすべて既定値のままにします。 **[完了]**をクリックします。  
   ![ODBC8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc8.png)
10. 問題なく動作することを確認するには、 **[データ ソースのテスト]**をクリックします。  
    ![ODBC9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc9.png)
11. テストの成功を確認します。  
    ![ODBC10](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc10.png)
12. ODBC 構成ファイルが [ファイル DSN] に表示されます。  
    ![ODBC11](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc11.png)

これで必要なファイルを用意できました。コネクタの作成を開始できます。

## <a name="create-the-generic-sql-connector"></a>Generic SQL コネクタの作成
1. Synchronization Service Manager UI で、**[コネクタ]** と **[作成]** を選択します。 **[Generic SQL (Microsoft)]** を選択し、わかりやすい名前を付けます。  
   ![Connector1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector1.png)
2. 前のセクションで作成した DSN ファイルを見つけ、サーバーにアップロードします。 データベースに接続するための資格情報を指定します。  
   ![Connector2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector2.png)
3. このチュートリアルでは、わかりやすく **User** と **Group** という 2 つのオブジェクト タイプを利用します。
   ![Connector3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector3.png)
4. 属性を検索する際、コネクタがテーブル自体を探し、これらの属性を検出するようにします。 **Users** は SQL の予約語であるため、角括弧 [ ] で指定する必要があります。  
   ![Connector4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector4.png)
5. 次に、アンカー属性と DN 属性を定義します。 **Users**には、username 属性と EmployeeID 属性の組み合わせを使用します。 **Group**には、GroupName を使用します (現実的ではありませんが、このチュートリアルでは十分です)。
   ![Connector5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector5.png)
6. 属性タイプの一部は SQL データベースで検出できません。 具体的には、参照属性タイプを検出できません。 グループ オブジェクト タイプについては、OwnerID と MemberID を参照に変更する必要があります。  
   ![Connector6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector6.png)
7. 前の手順で参照属性として選択した属性には、それらの値を参照するオブジェクト タイプが必要になります。 ここでは User オブジェクト タイプです。  
   ![Connector7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector7.png)
8. [Global Parameters (グローバル パラメーター)] ページで、[Delta Strategy (デルタ方針)] として **[透かし]** を選択します。 日付/時刻形式 **yyyy-MM-dd HH:mm:ss**を入力します。
   ![Connector8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector8.png)
9. **[Configure Partitions and Hierarchies (パーティションと階層の構成)]** ページで、両方のオブジェクト タイプを選択します。
   ![Connector9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector9.png)
10. **[オブジェクトの種類を選択]** と **[属性の選択]** で、両方のオブジェクト タイプとすべての属性を選択します。 **[Configure Anchors (アンカーの構成)]** ページで **[完了]** をクリックします。

## <a name="create-run-profiles"></a>実行プロファイルの作成
1. Synchronization Service Manager UI で、**[コネクタ]** と **[Configure Run Profiles (実行プロファイルの構成)]** を選択します。 **[新しいプロファイル]**をクリックします。 **フル インポート**から始めます。  
   ![Runprofile1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile1.png)
2. タイプに **[Full Import (Stage Only) (フル インポート (ステージのみ))]**を選択します。  
   ![Runprofile2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile2.png)
3. パーティションに **[OBJECT=User]**を選択します。  
   ![Runprofile3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile3.png)
4. **[テーブル]** を選択し、「**[USERS]**」と入力します。 [Multi-Valued Object Type (複数値オブジェクト タイプ)] セクションまで下へスクロールし、次の図のようにデータを入力します。 **[完了]** を選択し、手順を保存します。  
   ![Runprofile4a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4a.png)  
   ![Runprofile4b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4b.png)  
5. **[New Step (新しいステップ)]**を選択します。 今度は **[OBJECT=Group]**を選択します。 最後のページで、次の図のような構成を使用します。 **[完了]**をクリックします。  
   ![Runprofile5a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5a.png)  
   ![Runprofile5b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5b.png)  
6. 任意: 必要であれば、追加の実行プロファイルを構成できます。 このチュートリアルでは、フル インポートのみが使用されています。
7. **[OK]** をクリックし、実行プロファイルの変更を完了します。

## <a name="add-some-test-data-and-test-the-import"></a>テスト データを追加し、インポートをテストする
サンプル データベースにテスト データを入力します。 入力が完了したら、**[実行]** を選択し、**[Full import (フル インポート)]** を選択します。

以下は 2 つの電話番号を持つユーザーと何名かのメンバーが属するグループの例です。  
![cs1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs1.png)  
![cs2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs2.png)  

## <a name="appendix-a"></a>Azure BLOB ストレージ アカウントにデータを取得/アップロードする方法については、
**サンプル データベースを作成する SQL スクリプト**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```



<!--HONumber=Jan17_HO1-->


