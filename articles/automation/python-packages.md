---
title: Azure Automation で Python 2 パッケージを管理する
description: この記事では、Azure Automation での Python 2 パッケージの管理方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f20865b92df2a197410f209cf921c5e573723286
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850195"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Azure Automation で Python 2 パッケージを管理する

Azure Automation では、Azure および Linux Hybrid Runbook Worker で Python 2 Runbook を実行することができます。 Runbook の簡略化のために、Python パッケージを使用して必要なモジュールをインポートすることができます。 この記事では、Azure Automation での Python パッケージの管理および使用方法について説明します。

## <a name="import-packages"></a>パッケージをインポートする

Automation アカウントの **[共有リソース]** で **[Python 2 packages]\(Python 2 パッケージ\)** を選択します。 **[+ Python 2 パッケージを追加する]** をクリックします。

![Python パッケージを追加する](media/python-packages/add-python-package.png)

**[Python 2 パッケージの追加]** ページで、アップロードするローカル パッケージを選択します。 パッケージは `.whl` ファイルまたは `.tar.gz` ファイルを指定できます。 選択したら、 **[OK]** をクリックしてパッケージをアップロードします。

![Python パッケージを追加する](media/python-packages/upload-package.png)

パッケージがインポートされると、Automation アカウントの **[Python 2 packages]\(Python 2 パッケージ\)** ページに一覧表示されます。 パッケージを削除する必要がある場合は、そのパッケージを選択し、パッケージ ページの **[削除]** を選びます。

![パッケージ リスト](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>依存関係があるパッケージをインポートする

Azure Automation はインポート プロセス中に python パッケージの依存関係を解決しません。 すべての依存関係を含め、パッケージをインポートする方法は 2 つあります。 Automation アカウントにパッケージをインポートするには、以下の手順の 1 つのみを使用する必要があります。

### <a name="manually-download"></a>手動ダウンロード

[python2.7](https://www.python.org/downloads/release/latest/python2) および [pip](https://pip.pypa.io/en/stable/) がインストールされている Windows 64 ビット マシン上で、次のコマンドを実行してパッケージとそのすべての依存関係をダウンロードします。

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

パッケージがダウンロードされたら、それらをAutomation アカウントにインポートできます。

### <a name="runbook"></a>Runbook

ギャラリーの python Runbook [Import Python 2 packages from pypi into Azure Automation account](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) を Automation アカウントにインポートします。 [実行設定] が **[Azure]** に設定されていることを確認し、パラメーターを指定して Runbook を開始します。 Automation アカウントが機能するには、Runbook に実行アカウントが必要です。 各パラメーターについて、次の一覧と画像に示すように、必ずスイッチを指定して開始してください。

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![パッケージ リスト](media/python-packages/import-python-runbook.png)

Runbook では、ダウンロードするパッケージを指定できます。たとえば、`Azure` (4 番目のパラメーター) を指定すると、すべての Azure モジュールとそのすべての依存関係 (約 105 個) がダウンロードされます。

Runbook が完了したら、Automation アカウントの **[共有リソース]** の下にある **[Python 2 packages]\(Python 2 パッケージ\)** ページでパッケージが正しくインポートされたことを確認できます。

## <a name="use-a-package-in-a-runbook"></a>Runbook でパッケージを使用する

パッケージをインポートすると、Runbook で使用できるようになります。 次の例では、[Azure Automation ユーティリティ パッケージ](https://github.com/azureautomation/azure_automation_utility)を使用します。 このパッケージにより、Azure Automation で Python が使いやすくなります。 パッケージを使用するには、GitHub リポジトリの指示に従って、Runbook に追加します。その場合、`from azure_automation_utility import get_automation_runas_credential` などを使用して、RunAs アカウントを取得するための関数をインポートします。

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Runbook をオフラインで開発してテストする

Python 2 Runbook をオフラインで開発してテストするには、GitHub の [Azure Automation Python のエミュレートされた資産](https://github.com/azureautomation/python_emulated_assets)モジュールを使用できます。 このモジュールを使用すると、資格情報、変数、接続、および証明書などの共有リソースを参照できます。

## <a name="next-steps"></a>次の手順

Python 2 Runbook の使用を開始する場合は、[初めての Python2 Runbook](automation-first-runbook-textual-python2.md) に関するページを参照してください。
