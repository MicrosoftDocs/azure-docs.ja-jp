---
title: Azure DevTest Labs で環境を Azure Pipelines に統合する
description: Azure DevTest Labs 環境を Azure DevOps の継続的インテグレーション (CI) および継続的デリバリー (CD) パイプラインに統合する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be726b2a3f67fd3dada4fdc3cf794922a3c18d06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483025"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>環境を Azure DevOps CI/CD パイプラインに統合する
Azure DevOps Services (旧称 Visual Studio Team Services) にインストールされている Azure DevTest Labs タスク拡張機能を使用して、継続的インテグレーション (CI)/継続的デリバリー (CD) のビルド・リリース パイプラインを Azure DevTest Labs に簡単に統合できます。 これらの拡張機能により、特定のテスト タスク用に[環境](devtest-lab-test-env.md)をすばやく展開し、テストの終了時に削除するのがより簡単になります。 

この記事では、1 つの完全なパイプラインで、環境を作成して展開してから削除する方法を説明します。 通常は、これらの各タスクを独自のカスタムのビルド・テスト・展開パイプラインで個別に実行します。 この記事で使用されている拡張機能は、これらの [DTL VM の作成/削除タスク](devtest-lab-integrate-ci-cd.md)に追加されたものです。

- 環境の作成
- 環境の削除

## <a name="before-you-begin"></a>開始する前に
CI/CD パイプラインを Azure DevTest Labs に統合する前に、[Azure DevTest Labs タスク](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)拡張機能を Visual Studio Marketplace からインストールする必要があります。 

## <a name="create-and-configure-the-lab-for-environments"></a>環境用のラボの作成および構成
このセクションでは、Azure 環境の展開先となるラボを作成および構成する方法について説明します。

1. まだラボがない場合は[ラボを作成します](devtest-lab-create-lab.md)。 
2. 記事「[Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)」の手順に従って、ラボを構成し、環境テンプレートを作成します。
3. この例では、既存の Azure クイック スタート テンプレート [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/) を使用します。
4. **201-web-app-redis-cache-sql-database** フォルダーを、手順 2 で構成したリポジトリ内の **ArmTemplate** フォルダーにコピーします。

## <a name="create-a-release-definition"></a>リリース定義の作成
リリース定義を作成するには、次の手順を行います。

1.  **[ビルドとリリース]** ハブの **[リリース]** タブで、**プラス記号 (+)** ボタンを選択します。
2.  **[リリース定義の作成]** ウィンドウで、**[空]** テンプレートを選択し、**[次へ]** を選択します。
3.  **[後で選択]** を選択し、**[作成]** を選択して、1 つの既定の環境があり、リンクされた成果物がない新しいリリース定義を作成します。
4.  ショートカット メニューを開くには、新しいリリース定義で、環境名の横にある **省略記号 (...)** を選択し、**[変数の構成]** を選択します。
5.  リリース定義タスクで使用する変数用の **[構成 - 環境]** ウィンドウで、次の値を入力します。
1.  **[administratorLogin]** で、SQL 管理者のログイン名を入力します。
2.  **[AdministratorLoginPassword]** で、SQL 管理者のログインで使用されるパスワードを入力します。 "南京錠" アイコンを使用して、パスワードを非表示にしてセキュリティで保護します。
3.  **[databasename]** で、SQL Database 名を入力します。
4.  これらの変数はこのサンプル環境に固有のものであり、環境が異なれば変数も異なる場合があります。

## <a name="create-an-environment"></a>環境の作成
展開の次の段階は、開発またはテストの目的で使用される環境を作成することです。

1. リリース定義で、**[タスクの追加]** を選択します。
2. **[タスク]** タブで、[Azure DevTest Labs Create Environment]\(Azure DevTest Labs: 環境の作成\) タスクを追加します。 次に示すようにタスクを構成します。
    1. **[Azure RM サブスクリプション]** で、**[利用可能な Azure サービス接続]** の一覧から接続を選択するか、さらに制限されたアクセス許可を持つ Azure サブスクリプションへの接続を作成します。 詳しくは、「[Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints)」(Azure Resource Manager のサービス エンドポイント) をご覧ください。
2. **[ラボ名]** で、先ほど作成したインスタンスの名前を選択します*。
3. **[リポジトリ名]** で、Resource Manager テンプレート (201) がプッシュされたリポジトリを選択します*。
4. **[テンプレート名]** で、ソース コード リポジトリに保存した環境の名前を選択します*。 
5. **[ラボ名]**、**[リポジトリ名]**、**[テンプレート名]** は、Azure リソース ID のわかりやすい表現です。 フレンドリ名を手動で入力するとエラーが発生します。ドロップダウン リストを使用して情報を選択してください。
6. **[環境名]** で、ラボ内の環境のインスタンスを一意に識別する名前を入力します。  ラボ内で一意にする必要があります。
7. **[パラメーター ファイル]** と **[パラメーター]** を使用すると、カスタム パラメーターを環境に渡すことができます。 どちらか一方を使用しても両方を使用してもパラメーター値を設定できます。 この例では、[パラメーター] セクションを使用します。 環境内に定義した変数の名前を使用します。次に例を示します。`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. 環境テンプレート内の情報は、テンプレートの出力セクションで渡すことができます。 「**Create output variables based on the environment template output**」(環境テンプレートの出力に基づいて出力変数を作成する) を確認し、他のタスクがデータを使用できるようにしてください。 `$(Reference name.Output Name)` は推奨されるパターンです。 たとえば、参照名が DTL で、テンプレート内の出力名が location の場合、変数は `$(DTL.location)` になります。

## <a name="delete-the-environment"></a>環境の削除
最後の段階では、Azure DevTest Labs インスタンスに展開した環境を削除します。 通常は、開発タスクを実行した後、または展開したリソースに対して必要なテストを実行した後に、環境を削除します。

リリース定義で、**[タスクの追加]** を選択し、**[展開]** タブで、**[Azure DevTest Labs Delete Environment]\(Azure DevTest Labs: 環境の削除\)** タスクを追加します。 SAP コネクタを次のように構成します。

1. VM を削除するには、「[Azure DevTest Labs タスク](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)」を参照してください。
    1. **[Azure RM サブスクリプション]** で、**[利用可能な Azure サービス接続]** の一覧から接続を選択するか、さらに制限されたアクセス許可を持つ Azure サブスクリプションへの接続を作成します。 詳しくは、「[Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints)」(Azure Resource Manager のサービス エンドポイント) をご覧ください。
    2. **[ラボ名]** で、環境が存在するラボを選択します。
    3. **[環境名]** で、削除する環境の名前を入力します。
2. リリース定義の名前を入力し、それを保存します。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。 
- [Resource Manager テンプレートを使用してマルチ VM 環境を作成する](devtest-lab-create-environment-from-arm.md)。
- [DevTest Labs GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)にある、DevTest Labs 自動化のためのクイック スタート Resource Manager テンプレート。
- [VSTS のトラブルシューティングのページ](/azure/devops/pipelines/troubleshooting)

