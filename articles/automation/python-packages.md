---
title: Azure Automation で Python 2 パッケージを管理する
description: この記事では、Azure Automation での Python 2 パッケージの管理方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 0b0fab40eb95fabd4b74e809c9e6cd6f6573f513
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852907"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Azure Automation で Python 2 パッケージを管理する

Azure Automation では、Azure および Linux Hybrid Runbook Worker で Python 2 Runbook を実行することができます。 Runbook の簡略化のために、Python パッケージを使用して必要なモジュールをインポートすることができます。 この記事では、Azure Automation での Python パッケージの管理および使用方法について説明します。

## <a name="import-packages"></a>パッケージをインポートする

Automation アカウントの **[共有リソース]** で **[Python 2 パッケージ]** を選択します。 **[+ Python 2 パッケージを追加する]** をクリックします。

![Python パッケージを追加する](media/python-packages/add-python-package.png)

[Python 2 パッケージの追加] ページで、アップロードするローカル パッケージを選択します。 パッケージは、 **.whl** または **.tar.gz** ファイルの場合があります。 パッケージを選択したら、 **[OK]** をクリックしてアップロードします。

![Python パッケージを追加する](media/python-packages/upload-package.png)

パッケージがインポートされると、Automation アカウントの [Python 2 パッケージ] ページに一覧表示されます。 パッケージを削除する必要がある場合は、パッケージを選択し、 **[削除]** をクリックします。

![パッケージ一覧](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>依存関係があるパッケージをインポートする

Azure Automation ではインポート プロセス中に Python パッケージの依存関係を解決しません。 すべての依存関係を含め、パッケージをインポートする方法は 2 つあります。 Automation アカウントにパッケージをインポートするには、次の手順の 1 つのみを使用する必要があります。

### <a name="manually-download"></a>手動ダウンロード

[Python2.7](https://www.python.org/downloads/release/latest/python2) および [pip](https://pip.pypa.io/en/stable/) がインストールされている Windows 64 ビット マシン上で、次のコマンドを実行してパッケージとそのすべての依存関係をダウンロードします。

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

パッケージがダウンロードされたら、それらをAutomation アカウントにインポートできます。

### <a name="runbook"></a>Runbook

 Runbook を取得するには、ギャラリーから [Import Python 2 packages from pypi into Azure Automation account](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509) を Automation アカウントにインポートします。 [実行設定] が **[Azure]** に設定されていることを確認し、パラメーターを指定して Runbook を開始します。 Automation アカウントが機能するためには、Runbook に実行アカウントが必要です。 各パラメーターについて、次の一覧と画像に示すように、必ずスイッチを指定して開始してください。

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![パッケージ一覧](media/python-packages/import-python-runbook.png)

Runbook では、ダウンロードするパッケージを指定できます。 たとえば、`Azure` パラメーターを使用すると、すべての Azure モジュールとすべての依存関係 (約 105 個) がダウンロードされます。

Runbook が完了したら、Automation アカウントの **[共有リソース]** の下にある **[Python 2 パッケージ]** を調べて、パッケージが正しくインポートされていることを確認できます。

## <a name="use-a-package-in-a-runbook"></a>Runbook でパッケージを使用する

パッケージがインポートされたら、それを Runbook で使用できます。 次の例では、[Azure Automation ユーティリティ パッケージ](https://github.com/azureautomation/azure_automation_utility)を使用します。 このパッケージにより、Azure Automation で Python が使いやすくなります。 パッケージを使用するには、GitHub リポジトリの指示に従って、Runbook に追加します。 たとえば、`from azure_automation_utility import get_automation_runas_credential` を使用して、実行アカウントを取得するための関数をインポートできます。

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

## <a name="next-steps"></a>次のステップ

Python Runbook を準備するには、「[Python Runbook を作成する](learn/automation-tutorial-runbook-textual-python2.md)」を参照してください。
