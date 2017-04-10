---
title: "Azure Automation と Visual Stuido Team Services ソース管理の統合 | Microsoft Docs"
description: "Azure Automation アカウントと Visual Stuido Team Services ソース管理の統合を設定するシナリオについて説明します。"
services: automation
documentationcenter: 
author: eamono
manager: 
editor: 
keywords: "azure powershell, VSTS, ソース管理, 自動化"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
translationtype: Human Translation
ms.sourcegitcommit: f68563587d375dde22fd1ca45591fd49042533d0
ms.openlocfilehash: 01f9c01c9e04e02dbb548b68cf99684ba6ddd57e
ms.lasthandoff: 02/21/2017

---
# <a name="azure-automation-scenario---automation-source-control-integration-with-visual-studio-team-services"></a>Azure Automation のシナリオ - Automation ソース管理と Visual Studio Team Services の統合

このシナリオでは、ソース管理下にある Azure Automation の Runbook または DSC 構成の管理に使用している Visual Studio Team Services プロジェクトがあります。
この記事では、チェックインごとに継続的インテグレーションが発生するように、VSTS と Azure Automation 環境を統合する方法について説明します。

## <a name="getting-the-scenario"></a>シナリオの取得

このシナリオは、Azure Portal の [Runbook ギャラリー](automation-runbook-gallery.md)から直接インポート、または [PowerShell ギャラリー](https://www.powershellgallery.com)からダウンロードできる&2; つの PowerShell Runbook で構成されています。

### <a name="runbooks"></a>Runbook

Runbook | 説明| 
--------|------------|
Sync-VSTS | チェックインの処理が完了したら、VSTS ソース管理から Runbook または構成をインポートします。 手動で実行した場合、すべての Runbook または構成をインポートして Automation アカウントに発行します。| 
Sync-VSTSGit | チェックインの処理が完了したら、Git ソース管理下にある VSTS から Runbook または構成をインポートします。 手動で実行した場合、すべての Runbook または構成をインポートして Automation アカウントに発行します。|

### <a name="variables"></a>変数

変数 | 説明|
-----------|------------|
VSToken | 作成する変数資産 (VSTS 個人用アクセス トークンを含む) をセキュリティで保護します。 VSTS 個人用アクセス トークンを作成する方法は、[VSTS 認証に関するページ](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview)で確認できます。 
## <a name="installing-and-configuring-this-scenario"></a>このシナリオのインストールと構成

Runbook または構成を Automation アカウントに同期するために使用する VSTS で、[個人用アクセス トークン](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview)を作成します。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Runbook が VSTS に対して認証を行い、Runbook または構成を Automation アカウントに同期できるように、個人用アクセス トークンを保持する Automation アカウントで、[セキュリティで保護された変数](automation-variables.md)を作成します。 これには VSToken という名前を付けることができます。 

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Runbook または構成を Automation アカウントに同期する Runbook をインポートします。 VSTS ソース管理または Git を使用した VSTS を使用して Automation アカウントにデプロイするかどうかに応じて、PowerShellGallery.com の [VSTS サンプル Runbook](https://www.powershellgallery.com/packages/Sync-VSTS/1.0/DisplayScript) または [Git を使用した VSTS サンプル Runbook] (https://www.powershellgallery.com/packages/Sync-VSTSGit/1.0/DisplayScript) を使用することができます。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

これで、この Runbook を[発行](automation-creating-importing-runbook.md#publishing-a-runbook)できるようになったので、Webhook を作成できます。 
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

この Sync-VSTS Runbook の [Webhook](automation-webhooks.md) を作成し、次に示すようにパラメーターを入力します。 VSTS のサービス フックで必要なので、Webhook の URL をコピーします。 VSAccessTokenVariableName は、個人用アクセス トークンを保持するために前に作成したセキュリティで保護された変数の名前 (VSToken) です。 

VSTS (Sync-VSTS.ps1) と統合すると、次のパラメーターを取得します。
### <a name="sync-vsts-parameters"></a>Sync-VSTS パラメーター

パラメーター | Description| 
--------|------------|
WebhookData | VSTS サービス フックから送信されたチェックイン情報が含まれます。 このパラメーターは空白のままにする必要があります。| 
ResourceGroup | Automation アカウントがあるリソース グループの名前です。|
AutomationAccountName | VSTS と同期する Automation アカウントの名前です。|
VSFolder | Runbook と構成が存在する VSTS のフォルダーの名前です。|
VSAccount | Visual Studio Team Services アカウントの名前です。| 
VSAccessTokenVariableName | VSTS 個人用アクセス トークンを保持する、セキュリティで保護された変数 (VSToken) の名前です。| 


![](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

GIT (Sync-VSTSGit.ps1) を使用した VSTS を使用している場合、次のパラメーターを取得します。

パラメーター | 説明|
--------|------------|
WebhookData | VSTS サービス フックから送信されたチェックイン情報が含まれます。 このパラメーターは空白のままにする必要があります。| ResourceGroup | Automation アカウントがあるリソース グループの名前です。|
AutomationAccountName | VSTS と同期する Automation アカウントの名前です。|
VSAccount | Visual Studio Team Services アカウントの名前です。|
VSProject | Runbook と構成が存在する VSTS のプロジェクトの名前です。|
GitRepo | Git リポジトリの名前です。|
GitBranch | VSTS Git リポジトリの分岐の名前です。|
フォルダー | VSTS Git 分岐のフォルダーの名前です。|
VSAccessTokenVariableName | VSTS 個人用アクセス トークンを保持する、セキュリティで保護された変数 (VSToken) の名前です。|

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

コードのチェックイン時にこの Webhook をトリガーするフォルダーへのチェックインの VSTS で、サービス フックを作成します。 新しいサブスクリプションを作成するとき、統合する Web フックをサービスとして選択します。 サービス フックの詳細については、[VSTS サービス フックのドキュメント](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/get-started)を参照してください。
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

これで、VSTS への Runbook と構成のすべてのチェックインを実行して、これらを Automation アカウントに自動的に同期することができます。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

この Runbook が VSTS によってトリガーされず、手動で実行する場合は、webhookdata パラメーターを空のままにすることができ、これにより指定した VSTS フォルダーから完全同期が実行されます。

シナリオを削除する場合、VSTS からサービス フックを削除し、Runbook を削除して VSToken 変数を削除します。

