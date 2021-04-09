---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93bfb515c46413400ecd47105de378c7b677cecd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564962"
---
## <a name="prepare-your-repository"></a>リポジトリを準備する

Azure App Service ビルド サーバーから自動ビルドを取得するには、リポジトリのルートに、プロジェクトの適切なファイルがあることを確認してください。

| ランタイム | ルート ディレクトリのファイル |
|-|-|
| ASP.NET (Windows のみ) | _*.sln_、 _*.csproj_、または _default.aspx_ |
| ASP.NET Core | _*.sln_ または _*.csproj_ |
| PHP | _index.php_ |
| Ruby (Linux のみ) | _Gemfile_ |
| Node.js | _server.js_、_app.js_、またはスタート スクリプトを含む _package.json_ |
| Python | _\*.py_、_requirements.txt_、または _runtime.txt_ |
| HTML | _default.htm_、_default.html_、_default.asp_、_index.htm_、_index.html_、または _iisstart.htm_ |
| WebJobs | _\<job_name>/run.\<extension>_ これは _App\_Data/jobs/continuous_ (継続的 WebJobs の場合) または _App\_Data/jobs/triggered_ (トリガーされた WebJobs の場合) の下にあります。 詳細については、[Kudu WebJobs のドキュメント](https://github.com/projectkudu/kudu/wiki/WebJobs)をご覧ください。 |
| 関数 | [Azure Functions の継続的なデプロイ](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)に関するページをご覧ください。 |

デプロイをカスタマイズするには、 *.deployment* ファイルをリポジトリのルートに含めます。 詳細については、[デプロイのカスタマイズ](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)に関するページおよび「[Custom deployment script (カスタム デプロイ スクリプト)](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)」を参照してください。

> [!NOTE]
> Visual Studio で開発する場合は、[Visual Studio にリポジトリを自動作成](/azure/devops/repos/git/creatingrepo?tabs=visual-studio)させてください。 プロジェクトは Git を使用してすぐにデプロイできます。
>

