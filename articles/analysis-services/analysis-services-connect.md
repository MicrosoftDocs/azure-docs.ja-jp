---
title: "Azure Analysis Services からデータを取得 | Microsoft Docs"
description: "Azure の Analysis Services サーバーに接続してデータを取得する方法を説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/13/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: eccc181e2203a97de58005f9597613775338ebcc
ms.lasthandoff: 03/25/2017


---
# <a name="get-data-from-azure-analysis-services"></a>Azure Analysis Services からデータを取得

Azure でサーバーを作成して、表形式モデルを展開すると、社内のユーザーはすぐに接続してデータの分析を開始できるようになります。

## <a name="data-providers-aka-client-libraries"></a>データ プロバイダー (クライアント ライブラリ)

Power BI Desktop や Microsoft Excel などのクライアント アプリケーションでは、最新の AMO、ADOMD.NET、OLEDB の各プロバイダーを使用して Analysis Services に接続します。 一部の古いバージョンの Excel やカスタム アプリケーションでは、Azure Analysis Services に接続するために最新のデータ プロバイダーをインストールすることが必要な場合があります。 詳細については、[データ プロバイダー](analysis-services-data-providers.md)に関するページをご覧ください。

## <a name="server-name"></a>サーバー名

Analysis Services サーバーを Azure に作成するとき、一意の名前と、サーバーが作成されるリージョンを指定します。 接続にサーバー名を指定する場合、サーバーの命名方法は次のとおりです。

```
<protocol>://<region>/<servername>
```
 ここで、protocol は文字列 **asazure**、region はサーバーが作成されたリージョンの Uri (たとえば、米国西部の場合は westus.asazure.windows.net)、servername はそのリージョンの一意のサーバー名です。

## <a name="get-the-server-name"></a>サーバー名の取得

接続する前に、サーバー名を取得する必要があります。 **Azure Portal** でサーバーを選び、**[概要]** > **[サーバー名]** のサーバー名全体をコピーします。 組織内の他のユーザーもこのサーバーに接続する場合は、このサーバー名を他のユーザーと共有できます。 サーバー名を指定するときは、パス全体を使用する必要があります。

![Azure でサーバー名を取得する](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop での接続

> [!NOTE]
> この機能はプレビューです。
> 
> 

1. [Power BI Desktop](https://powerbi.microsoft.com/desktop/) で、**[データの取得]** > **[Azure]** > **[Microsoft Azure Analysis Services データベース]** をクリックします。
2. **[サーバー]** に、クリップボードからサーバー名を貼り付けます。
3. **[データベース]** には、接続先の表形式モデル データベースまたはパースペクティブの名前がわかっている場合はその名前を貼り付けます。 それ以外の場合、このフィールドは空にしておくことができます。 次の画面でデータベースまたはパースペクティブを選択できます。
4. 既定の **[ライブ接続]** オプションが選択された状態のままで、**[接続]** をクリックします。 アカウントの入力を求められた場合は、会社のアカウントを入力します。
5. **[ナビゲーター]** で、サーバーを展開し、接続先のモデルまたはパースペクティブを選択してから **[接続]** をクリックします。 モデルまたはパースペクティブをクリックすると、そのビューのすべてのオブジェクトが表示されます。

## <a name="connect-in-power-bi"></a>Power BI での接続

1. サーバー上のモデルにライブ接続できる Power BI Desktop ファイルを作成します。
2. [Power BI](https://powerbi.microsoft.com) で、**[データの取得]** > **[ファイル]** をクリックします。 ファイルを検索して選択します。

## <a name="connect-in-excel"></a>Excel での接続

Excel での Azure Analysis Services サーバーへの接続は、Excel 2016 の Get Data または以前のバージョンの Power Query を使用してサポートされます。 [MSOLAP.7 プロバイダー](analysis-services-data-providers.md)が必要です。 Power Pivot での [テーブルのインポート] ウィザードを使用した接続はサポートされません。

> [!NOTE]
> 一部の会社は、段階的提供チャネルで Office 365 の更新プログラムをデプロイします。つまり、現在のバージョンからの更新が最長で 4 か月遅れることになります。 Excel 2016 バージョン 1609.7369.2115 以前または Excel 2013 の場合は、.odc ファイルを作成して、MSOLAP.7 プロバイダーを手動で更新してサーバーに接続できます。 詳細については、「[Office データ接続 (.odc) ファイルの作成](analysis-services-odc.md)」を参照してください。
> 
> 

**Excel 2016 から接続するには**

1. Excel 2016 では、**[データ]** リボンで **[外部データの取り込み]** > **[その他のデータ ソース]** > **[From Analysis Services (Analysis Services)]** をクリックします。
2. データ接続ウィザードで、**[サーバー名]** にクリップボードのサーバー名を貼り付けます。 そして **[ログオン資格情報]** で **[以下のユーザー名とパスワードを使用する]** を選択し、組織でのユーザー名 (たとえば nancy@adventureworks.com) とパスワードを入力します。

    ![Excel での接続、ログオン](./media/analysis-services-connect/aas-connect-excel-logon.png)
3. **[データベースとテーブルの選択]** で、データベースとモデルまたはパースペクティブを選択し、**[完了]** をクリックします。
   
    ![Excel での接続、モデルの選択](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>接続文字列

表形式オブジェクト モデルを使用して Azure Analysis Services に接続するときは、次の接続文字列の形式を使用します。

###### <a name="integrated-azure-active-directory-authentication"></a>統合 Azure Active Directory 認証

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
統合認証では、Azure Active Directory 資格情報キャッシュがあればそれが使用されます。 ない場合は、Azure ログイン ウィンドウが表示されます。

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>ユーザー名とパスワードによる Azure Active Directory の認証

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="next-steps"></a>次のステップ

[サーバーの管理](analysis-services-manage.md)


