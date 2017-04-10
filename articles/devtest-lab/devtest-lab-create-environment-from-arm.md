---
title: "Azure Resource Manager テンプレートを使った複数 VM 環境の作成 | Microsoft Docs"
description: "Azure Resource Manager テンプレートから Azure DevTest Labs で複数 VM の環境を作成する方法について説明します"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: c17ea1f078c424d855109da07113a4c2f8f84ab3
ms.lasthandoff: 03/15/2017


---

# <a name="create-multi-vm-environments-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使った複数 VM 環境の作成

[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) を使用すると、簡単に [VM を作成してラボに追加する](./devtest-lab-add-vm-with-artifacts.md)ことができます。 この方法は、一度に&1; つの VM を作成する場合に適してします。 ところが、環境に複数の VM が含まれる場合は、各 VM を個別に作成する必要があります。 多層 Web アプリや SharePoint ファームのようなシナリオでは、シングル ステップで複数の VM を作成できるメカニズムが必要です。 Azure Resource Manager テンプレートを使用することで、Azure のソリューションのインフラストラクチャと構成を定義して、複数の VM を一貫した状態で繰り返しデプロイできるようになりました。 この機能には次のような利点があります。

- Azure Resource Manager テンプレートが、ソース管理リポジトリ (GitHub や Team Services Git) から直接読み込まれます。
- 構成したら、ユーザーは、他の種類の [VM ベース](./devtest-lab-comparing-vm-base-image-types.md)を使用してできることとして Azure Portal から Azure Resource Manager テンプレートを選択して環境を作成できます。
- Azure PaaS のリソースを、IaaS VM だけでなく Azure Resource Manager テンプレートから環境にプロビジョニングできます。
- 環境のコストを、他の種類のベースによって作成された個々の VM だけでなくラボで追跡できます。
- ユーザーは、単一のラボの VM と同じ VM ポリシー コントロールをさまざまな環境で使用できます。

## <a name="configure-azure-resource-manager-template-repositories"></a>Azure Resource Manager テンプレート リポジトリを構成する

コードとしてのインフラストラクチャとコードとしての構成のベスト プラクティスの&1; つとして、環境テンプレートをソース管理で管理する必要があります。 Azure DevTest Labs は、このベスト プラクティスに従って、GitHub または VSTS の Git リポジトリから直接すべての Azure Resource Manager テンプレートを読み込みます。 この場合、リポジトリ内で Azure Resource Manager テンプレートを整理するためのルールがいくつかあります。

- マスター テンプレート ファイルの名前を `azuredeploy.json` にする必要があります。 

    ![主要な Azure Resource Manager テンプレート ファイル](./media/devtest-lab-create-environment-from-arm/master-template.png)

- パラメーター ファイルで定義したパラメーター値を使用する場合は、パラメーター ファイルの名前を `azuredeploy.parameters.json` にする必要があります。
- メタデータを定義して、テンプレートの表示名と説明を指定することができます。 このメタデータは、`metadata.json` はという名前のファイルに格納する必要があります。 次のメタデータ ファイルの例で、表示名と説明を指定する方法を示します。 

```json
{
 
"itemDisplayName": "<your template name>",
 
"description": "<description of the template>"
 
}
```

次の手順では、Azure Portal を使用してラボにリポジトリを追加する方法を説明します。 

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で目的のラボを選択します。   
1. ラボのブレードで、**[Configuration and Policies (構成とポリシー)]** を選択します。

    ![構成とポリシー](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. **[Configuration and Policies (構成とポリシー)]** の設定一覧で、**[リポジトリ]** を選択します。 **[リポジトリ]** ブレードには、ラボに追加されたリポジトリが一覧表示されます。 `Public Repo` という名前のリポジトリがすべてのラボに自動的に生成され、複数の VM アーティファクトを格納する [DevTest ラボ GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)に接続され、使用されます。

    ![Public Repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. **[追加+]** を選択し、Azure Resource Manager テンプレートのリポジトリを追加します。
1. 2 つ目の **[リポジトリ]** ブレードが開いたら、必要な情報を次のように入力します。
    - **名前** - ラボで使用するリポジトリ名を入力します。
    - **Git クローン URL** - GitHub または Visual Studio Team Services の GIT HTTPS クローン URL を入力します。  
    - **分岐** - Azure Resource Manager テンプレートの定義にアクセスするための分岐名を入力します。 
    - **個人用アクセス トークン** - 個人用アクセス トークンは、リポジトリに安全にアクセスするために使用されます。 Visual Studio Team Services からトークンを取得するには、**&lt;自分の名前>、[マイ プロファイル]、[セキュリティ]、[Public access token (パブリック アクセス トークン)]** の順に選択します。 GitHub からトークンを入手するには、アバターを選択した後に、**[設定]、[Public access token (パブリック アクセス トークン)]** の順に選択します。 
    - **フォルダー パス** -&2; つの入力フィールドのいずれかを使用して、アーティファクトの定義 (最初の入力フィールド) または Azure Resource Manager テンプレートの定義に、フォワード スラッシュ (/) で始まり、Git クローン URI に対して相対的なフォルダー パスを入力します。   
    
        ![Public Repo](./media/devtest-lab-create-environment-from-arm/repo-values.png)

1. すべての必須フィールドを入力し、検証に合格したら、**[保存]** を選択します。

次のセクションでは、Azure Resource Manager テンプレートから環境を作成する手順を説明します。

## <a name="create-an-environment-from-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートから環境を作成する

ラボで Azure Resource Manager テンプレート リポジトリが構成されると、ラボのユーザーは Azure Portal で次の手順を使用して環境を作成できます。

1. [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で目的のラボを選択します。   
1. ラボのブレードで、**[追加+]** を選択します。
1. **[ベースの選択]** ブレードに、使用できるベース イメージが表示されます。Azure Resource Manager テンプレートがその先頭に表示されます。 目的の Azure Resource Manager テンプレートを選択します。

    ![ベースの選択](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. **[追加]** ブレードで、**[環境名]** に値を入力します。 環境名は、ラボでユーザーに表示される内容です。 その他の入力フィールドは、Azure Resource Manager テンプレートで定義します。 既定値がテンプレートで定義されている場合、または `azuredeploy.parameter.json` ファイルが存在する場合は、これらの入力フィールドに既定値が表示されます。 "*セキュリティで保護された文字列*" 型のパラメーターの場合、ラボの[個人のシークレット ストア](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)に格納されたシークレットを使用することができます。

    ![[追加] ブレード](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > 値を指定した場合でも、空の値として表示されるパラメーター値がいくつかあります。 そのため、ユーザーが Azure Resource Manager テンプレートでこれらの値をパラメーターに割り当てた場合でも、DevTest Labs で値が表示されません。代わりに、環境の作成時にラボのユーザーが値を入力する必要がある場所には、入力フィールドが空白で示されます。
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. **[追加]** を選択して環境を作成します。 環境でプロビジョニングが開始され、**[仮想マシン]** の一覧に状態が即座に表示されます。 Azure Resource Manager テンプレートで定義されているすべてのリソースをプロビジョニングするために、新しいリソース グループがラボによって自動的に作成されます。
1. 環境が作成されたら、**[仮想マシン]** の一覧で環境を選択してリソース グループ ブレードを開き、環境内にプロビジョニングされたリソースを参照します。
    
    ![[仮想マシン] の一覧](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 任意の環境をクリックして、アーティファクトの適用、データ ディスクのアタッチ、自動シャット ダウン時刻の変更など、使用可能なアクションを参照します。

    ![環境のアクション](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="next-steps"></a>次のステップ
* VM が作成されたら、その VM のブレードで **[接続]** を選択して VM に接続できます。
* [Azure クイックスタート テンプレート ギャラリーから Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates)を検索します

