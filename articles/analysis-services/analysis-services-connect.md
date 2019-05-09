---
title: Azure Analysis Services サーバーに接続する | Microsoft Docs
description: Azure の Analysis Services サーバーに接続してデータを取得する方法を説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9a8863189ee9cb63d86b157c0bbebb6fd16116b0
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669624"
---
# <a name="connecting-to-servers"></a>サーバーへの接続

この記事では、SQL Server Management Studio (SSMS) や SQL Server Data Tools (SSDT) などの管理アプリケーションとデータ モデリングを使ってサーバーに接続する方法について説明します。 または、Microsoft Excel、Power BI Desktop などのクライアント レポート作成アプリケーションやカスタム アプリケーションを使用することもできます。 Azure Analysis Services への接続には HTTPS が使用されます。

## <a name="client-libraries"></a>クライアント ライブラリ

[最新のクライアント ライブラリを入手する](analysis-services-data-providers.md)

サーバーへの接続全般に言えることですが、Analysis Services サーバーに接続してデータをやり取りするためには、最新の AMO、ADOMD.NET、OLEDB クライアント ライブラリが必要となります。 SSMS、SSDT、Excel 2016 以降、および Power BI では、毎月のリリースにより最新のクライアント ライブラリがインストールまたは更新されます。 ただし、場合によっては、アプリケーションの最新バージョンがないこともあります。 たとえば、ポリシーによって更新が先延ばしされたり、Office 365 の更新プログラムが段階的提供チャネルで提供されたりするケースがそれに該当します。

## <a name="server-name"></a>サーバー名

Analysis Services サーバーを Azure に作成するとき、一意の名前と、サーバーが作成されるリージョンを指定します。 接続にサーバー名を指定する場合、サーバーの命名方法は次のとおりです。

```
<protocol>://<region>/<servername>
```
 ここで、protocol は文字列 **asazure**、region はサーバーが作成された URI (たとえば、westus.asazure.windows.net)、servername はそのリージョンの一意のサーバー名です。

### <a name="get-the-server-name"></a>サーバー名の取得

**Azure Portal** でサーバーを選び、**[概要]** > **[サーバー名]** のサーバー名全体をコピーします。 組織内の他のユーザーもこのサーバーに接続する場合は、このサーバー名を他のユーザーと共有できます。 サーバー名を指定するときは、パス全体を使用する必要があります。

![Azure でサーバー名を取得する](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> 米国東部 2 リージョンのプロトコルは **aspaaseastus2** です。

## <a name="connection-string"></a>接続文字列

表形式オブジェクト モデルを使用して Azure Analysis Services に接続するときは、次の接続文字列の形式を使用します。

###### <a name="integrated-azure-active-directory-authentication"></a>統合 Azure Active Directory 認証

統合認証では、Azure Active Directory 資格情報キャッシュがあればそれが使用されます。 ない場合は、Azure ログイン ウィンドウが表示されます。

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>ユーザー名とパスワードによる Azure Active Directory の認証

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows 認証 (統合セキュリティ)

現在のプロセスを実行している Windows アカウントが使用されます。

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>.odc ファイルを使用した接続

以前のバージョンの Excel を使用するユーザーは、Office データ接続 (.odc) ファイルを使って Azure Analysis Services サーバーに接続することができます。 詳細については、「[Office データ接続 (.odc) ファイルの作成](analysis-services-odc.md)」を参照してください。


## <a name="next-steps"></a>次の手順

[Excel を使用した接続](analysis-services-connect-excel.md)    
[Power BI を使用した接続](analysis-services-connect-pbi.md)   
[サーバーの管理](analysis-services-manage.md)   

