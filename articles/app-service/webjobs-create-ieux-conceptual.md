---
title: Azure での Web ジョブ、バックグラウンド タスク
description: Web ジョブについて説明します。
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7714b090399b0b184e2e216ff6da7b10f2bf4386
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452273"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>Azure App Service で Web ジョブがバックグラウンド タスクを実行する

この記事では、実行可能ファイルまたはスクリプトをアップロードする Web ジョブを [Azure ポータル](https://portal.azure.com)を使用してデプロイする方法について説明します。 Visual Studio を使用した Web ジョブの開発とデプロイ方法については、[Visual Studio を使用した Web ジョブのデプロイ](webjobs-dotnet-deploy-vs.md)に関する記事を参照してください。

## <a name="overview"></a>概要
Web ジョブは、Web アプリ、API アプリ、またはモバイル アプリと同じインスタンスでプログラムやスクリプトを実行できる [Azure App Service](index.yml) の機能です。 Web ジョブの使用に追加コストはかかりません。

> [!IMPORTANT]
> Web ジョブは App Service on Linux ではまだサポートされていません。

Azure WebJobs SDK と Web ジョブを使用して、多くのプログラミング タスクを単純化できます。 詳細については、「 [Azure Web ジョブ SDK とは](https://github.com/Azure/azure-webjobs-sdk/wiki)」をご覧ください。

Azure Functions は、プログラムとスクリプトを実行する別の方法を提供します。 Web ジョブと Functions の比較については、「[Flow、Logic Apps、Functions、WebJobs の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)」を参照してください。

## <a name="webjob-types"></a>Web ジョブの種類

次の表で、*継続的* Web ジョブと *トリガーされる* Web ジョブの違いを説明します。


|継続的  |トリガー  |
|---------|---------|
| Web ジョブが作成されるとすぐに開始します。 ジョブが終了しないようにするため、プログラムまたはスクリプトは、その動作を無限ループ内で実行します。 ジョブが終了した場合でも、再開することができます。 | 手動またはスケジュールに従ってトリガーされたときにのみ開始します。 |
| Web アプリが実行されているすべてのインスタンスで実行されます。 必要に応じて、Web ジョブを 1 つのインスタンスに制限できます。 |Azure が負荷分散用に選択した 1 つのインスタンス上で実行されます。|
| リモート デバッグをサポートします。 | リモート デバッグをサポートしません。|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>Web ジョブをソース管理に追加する

アプリケーションでソース管理が構成されている場合は、ソース管理の統合の一環として Webjobs をデプロイする必要があります。 アプリケーションでソース管理を構成した後は、Azure portal から WebJob を追加することはできません。

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>スクリプトまたはプログラムでサポートされるファイルの種類

次のファイルの種類がサポートされます。

* .cmd、.bat、.exe (Windows コマンドを使用)
* .ps1 (PowerShell を使用)
* .sh (Bash を使用)
* .php (PHP を使用)
* .py (Python を使用)
* .js (Node.js を使用)
* .jar (Java を使用)

## <a name="next-steps"></a>次のステップ

* [Web ジョブを作成する](./webjobs-create-ieux.md)方法を学習する
* [Web ジョブ](./webjobs-create-ieux-view-log.md)のログ履歴を表示する
