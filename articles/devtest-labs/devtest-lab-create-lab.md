---
title: Azure DevTest Labs でのラボの作成 | Microsoft Docs
description: この記事では、Azure portal と Azure DevTest Labs を使用してラボを作成する手順について説明します。
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 9fa33a59dd35bfe3469f30f2349f8a08c45bd5e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92058345"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボの作成

Azure DevTest Labs におけるラボとは、Virtual Machines (VM) などのリソース グループを包含するインフラストラクチャであり、制限とクォータを指定することでこれらのリソースをより適切に管理することができます。 この記事では、Azure Portal を使用してラボを作成する手順を説明します。

## <a name="prerequisites"></a>前提条件

ラボを作成するには、次のものが必要です。

* Azure サブスクリプション。 Azure 購入オプションの詳細については、「[Azure の購入方法](https://azure.microsoft.com/pricing/purchase-options/)」または [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。 ラボを作成するには、サブスクリプションの所有者である必要があります。

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Get started with Azure DevTest Labs in minutes

次のリンクをクリックすると、Azure portal ページに移動し、Azure DevTest Labs での新しいラボの作成を開始することができます。

[Get started with Azure DevTest Labs in minutes](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>新しいアカウントの設定を入力する

**[Create a DevTest Labs]\(DevTest Labs の作成\)** ページで、次の設定を入力します。

> [!TIP]
> 各ページの下部に、**自動化用のテンプレートをダウンロード** できるリンクが表示されます。

### <a name="basic-settings"></a>[基本設定]

既定では、 **[基本設定]** タブが表示されます。 

![基本設定](./media/devtest-lab-create-lab/basic-settings.png)

次の値を入力します。

|名前|説明|
|---|---|
|**サブスクリプション** | 必須です。 ラボに関連付ける **[サブスクリプション]** を選択します。|
|**リソース グループ**| 必須です。 ラボの **リソース グループの名前** を入力します。 新しいものが存在しない場合は作成します。|
|**ラボ名**| 必須です。 ラボの **名前** を入力します。|
|**場所**|必須です。 ラボを格納する場所を選択します。|
|**パブリックな環境**| [パブリックな環境の構成と使用](devtest-lab-configure-use-public-environments.md)に関するページを参照してください。

### <a name="auto-shutdown-settings"></a>自動シャットダウンの設定

**[自動シャットダウン]** ページに切り替えて、その設定を確認します。 自動シャットダウンを使用すると、毎日スケジュールされた時刻にラボ内のすべてのマシンを自動的にシャットダウンできます。

![[自動シャットダウン] タブ](./media/devtest-lab-create-lab/auto-shutdown.png)

このページで、 **[自動シャットダウン]** を有効にし、ラボのすべての VM の自動シャットダウン用のパラメーターを定義できます。 自動シャットダウン機能は、主にコストを削減する機能で、VM をいつ自動的にシャットダウンするかを指定することができます。 自動シャットダウンの設定は、ラボの作成後に、「[Azure DevTest Labs でラボのすべてのポリシーを管理](./devtest-lab-set-lab-policy.md#set-auto-shutdown)」という記事に書かれている手順に従って変更することができます。

### <a name="networking"></a>ネットワーク

ラボを作成すると、既定のネットワークが自動的に作成されます。 **[ネットワーク]** タブに切り替えて、必要に応じて設定を変更または構成します。 たとえば、既存の仮想ネットワークを選択します。

![[ネットワーク] タブ ](./media/devtest-lab-create-lab/networking.png)

### <a name="tags"></a>タグ

カスタム タグを作成して、ラボで作成するすべてのリソースに追加する場合は、**[タグ]** の **[名前]** と **[値]** の情報を入力します。 タグを適用することによって、ラボのリソースをカテゴリ別に管理して整理することができます。 ラボの作成後にタグを追加する方法など、タグの詳細については、[ラボにタグを追加する方法](devtest-lab-add-tag.md)に関するページを参照してください。

![[タグ] タブ ](./media/devtest-lab-create-lab/tags.png)

### <a name="review-and-create"></a>確認と作成

完了したら、 **[作成]** を選択します。 ラボ作成プロセスの状態は、ポータル ページの右上にある **[通知]** 領域を見て監視できます。 

![[作成] タブ](./media/devtest-lab-create-lab/create-1.png)

## <a name="completed-the-creation"></a>作成の完了

完了すると、ページの下部と通知ウィンドウに **[リソースに移動]** ボタンが表示されます。 または、**[DevTest Labs]** ページを最新の情報に更新すると、ラボの一覧に新しく作成されたラボが表示されます。  

![サービスの作成](./media/devtest-lab-create-lab/create-2.png)

**[リソースに移動]** ボタンを押すと、新しい DevTest Labs アカウントのホームページが表示されます。

![リソース](./media/devtest-lab-create-lab/go-to-resource.png)

また、Azure portal で **DevTest Labs** を検索することもできます。 一覧から新しいアカウントを選択し、ホーム ページに移動します。 

![サービスの作成](./media/devtest-lab-create-lab/created.png)

## <a name="next-steps"></a>次のステップ

ラボを作成した後は、次の手順を考慮します。

* [ラボへのアクセスをセキュリティで保護する](devtest-lab-add-devtest-user.md)
* [ラボのポリシーを設定する](devtest-lab-set-lab-policy.md)
* [ラボ テンプレートを作成する](devtest-lab-create-template.md)
* [VM のカスタム アーティファクトを作成する](devtest-lab-artifact-author.md)
* [VM をラボに追加する](devtest-lab-add-vm.md)

