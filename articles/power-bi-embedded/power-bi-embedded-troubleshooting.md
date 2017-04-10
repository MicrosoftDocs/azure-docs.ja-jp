---
title: "Microsoft Power BI Embedded プレビューのトラブルシューティング"
description: "Microsoft Power BI Embedded プレビューのトラブルシューティング"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: c8aee652-ed8b-4c66-9c63-f798b7a655b4
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1f4cf4a04e7a921f8e11c94f74d53ac80c231933


---
# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Microsoft Power BI Embedded プレビューのトラブルシューティング
この記事では、**Power BI Embedded** のトラブルシューティングの方法に関する質問の回答を示します。

<a name="connection-string"/>

## <a name="setting-sql-server-connection-strings"></a>SQL Server 接続文字列の設定
SQL Server 接続文字列を設定するには、特定の形式に従う必要があります。 SQL Server の接続文字列の例を次に示します。

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

SQL Server 接続文字列の詳細については、次の記事をご覧ください。

* [SQL Server Connection Strings for ASP.NET Web Applications (ASP.NET Web アプリケーションの SQL Server 接続文字列)](https://msdn.microsoft.com/library/jj653752.aspx)
* [SqlConnection.ConnectionString Property (SqlConnection.ConnectionString プロパティ)](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>

## <a name="setting-credentials"></a>資格情報の設定
開発環境またはステージング環境用の資格情報 (ユーザー名とパスワードなど) がある場合は、運用環境のソリューションに合わせて資格情報を更新する必要があります。

## <a name="see-also"></a>関連項目
* [Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](power-bi-embedded-get-started-sample.md)
* [What is Microsoft Power BI Embedded (Microsoft Power BI Embedded について)](power-bi-embedded-what-is-power-bi-embedded.md)




<!--HONumber=Nov16_HO3-->


