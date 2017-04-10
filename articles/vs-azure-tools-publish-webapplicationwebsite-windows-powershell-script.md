---
title: "Publish-WebApplicationWebSite (Windows PowerShell スクリプト) | Microsoft Docs"
description: "Web プロジェクトを Azure Web サイトに発行する方法について説明します。 このスクリプトは、必要なリソースが Azure サブスクリプションに存在しない場合にそれらを作成します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6942ea67050b86dc8f5a4c9d71ebd8ebd83ad0a2


---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publish-WebApplicationWebSite (Windows PowerShell スクリプト)
## <a name="syntax"></a>構文
Web プロジェクトを Azure Web サイトに発行します。 このスクリプトは、必要なリソースが Azure サブスクリプションに存在しない場合にそれらを作成します。

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>構成
デプロイの詳細が記述されている JSON 構成ファイルへのパス。

| パラメーター | 既定値 |
| --- | --- |
| 別名 |なし |
| 必須 |true |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

## <a name="subscriptionname"></a>SubscriptionName
Web サイトを作成する Azure サブスクリプションの名前。

| パラメーター | 既定値 |
| --- | --- |
| 別名 |なし |
| 必須 |false |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

## <a name="webdeploypackage"></a>WebDeployPackage
Web サイトに発行する Web デプロイ パッケージへのパス。 Visual Studio で Web の発行ウィザードを使用して、このパッケージを作成できます。 詳細については、「 [Azure Cloud Services と ASP.NET を使ってみる](http://go.microsoft.com/fwlink/p/?LinkID=623089)」を参照してください。

| パラメーター | 既定値 |
| --- | --- |
| 別名 |なし |
| 必須 |false |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
Azure の SQL データベースのユーザー名およびパスワード。

| パラメーター | 既定値 |
| --- | --- |
| 別名 |なし |
| 必須 |false |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
true の場合、スクリプトからのメッセージは出力ストリームに出力されます。

| パラメーター | 既定値 |
| --- | --- |
| 別名 |なし |
| 必須 |false |
| 位置 |named |
| 既定値 |false |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

## <a name="remarks"></a>解説
スクリプトを使用して開発とテストの環境を作成する方法の詳細については、「 [Windows PowerShell スクリプトを使用した開発環境およびテスト環境の発行](vs-azure-tools-publishing-using-powershell-scripts.md)」をご覧ください。

JSON 構成ファイルではデプロイ対象の詳細が指定されます。 この中には、Web サイトの名前やユーザー名など、プロジェクトの作成時に指定した情報が含まれます。 また、プロビジョニングを行うデータベースがある場合はそれも含まれます。 次のコードは JSON 構成ファイルの例を示しています。

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

JSON 構成ファイルは、デプロイ対象を変更するように編集できます。 webSite のセクションは必須ですが、データベースのセクションは省略可能です。

## <a name="next-steps"></a>次のステップ
詳細については、「 [Publish-WebApplicationVM (Windows PowerShell スクリプト)](vs-azure-tools-publish-webapplicationvm.md)




<!--HONumber=Nov16_HO3-->


