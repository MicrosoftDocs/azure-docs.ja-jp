---
title: インベントリ収集による Azure 仮想マシンの管理 | Microsoft Docs
description: インベントリ収集による仮想マシンの管理
services: automation
ms.service: automation
ms.subservice: change-inventory-management
keywords: インベントリ, オートメーション, 変更, 追跡
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/06/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59f36595e0b6cc8b9d9ea0669c9ecb5be1e74b42
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441391"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>インベントリ収集による Azure 仮想マシンの管理

Azure 仮想マシンに対するインベントリの追跡は、その仮想マシンのリソース ページから有効にすることができます。 コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集し、表示することができます。 この方法では、ブラウザーベースのユーザー インターフェイスで、インベントリ収集を設定および構成できます。

## <a name="before-you-begin"></a>開始する前に

Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

この記事では、ソリューションを構成した VM があることを前提にしています。 Azure 仮想マシンをお持ちでない場合は、[仮想マシンを作成](../virtual-machines/windows/quick-create-portal.md)してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>仮想マシン リソース ページからインベントリ収集を有効にする

1. Azure Portal の左ウィンドウで **[仮想マシン]** を選択します。
2. 仮想マシンの一覧で仮想マシンを選択します。
3. **[リソース]** メニューの **[操作]** で **[インベントリ]** を選択します。
4. データ ログを格納する Log Analytics ワークスペースを選択します。
    そのリージョンで利用可能なワークスペースがない場合は、既定のワークスペースと Automation アカウントを作成するよう求められます。
5. コンピューターのオンボードを開始するために、**[Enable]\(有効化\)** を選択します。

   ![オンボード オプションの表示](./media/automation-vm-inventory/inventory-onboarding-options.png)

    ステータス バーで、ソリューションが有効にされていることが通知されます。 このプロセスには最大 15 分かかる可能性があります。 この間、ウィンドウは閉じても開いたままでもかまいません。開いたままにした場合は、ソリューションが有効になると、そのことを伝えるメッセージが表示されます。 デプロイの状態は通知ペインから監視することができます。

   ![オンボードの直後にインベントリ ソリューションを表示する](./media/automation-vm-inventory/inventory-onboarded.png)

デプロイが完了すると、ステータス バーは表示されなくなります。 システムがまだインベントリ データを収集しているため、データが表示されない可能性があります。 データの完全な収集には 24 時間かかることがあります。

## <a name="configure-your-inventory-settings"></a>インベントリ設定を構成する

既定では、ソフトウェア、Windows サービス、Linux デーモンが収集対象として構成されています。 Windows レジストリとファイル インベントリを収集するには、インベントリ収集設定を構成します。

1. **[インベントリ]** ビューで、ウィンドウ上部にある **[設定の編集]** ボタンをクリックします。
2. 新しい収集設定を追加するには、**[Windows レジストリ]**、**[Windows ファイル]**、**[Linux ファイル]** の各タブを選択して、追加する設定カテゴリに移動します。
3. 適切なカテゴリを選択して、ウィンドウの上部で **[追加]** をクリックします。

次の表に、さまざまなカテゴリに対して構成可能な各プロパティの情報を示します。

### <a name="windows-registry"></a>Windows レジストリ

|プロパティ  |説明  |
|---------|---------|
|Enabled     | 設定が適用されるかどうかを決定します。        |
|Item Name     | 追跡するファイルのフレンドリ名。        |
|グループ     | ファイルを論理的にグループ化するためのグループ名。        |
|Windows レジストリ キー   | ファイル確認のためのパス。その例を次に示します。"HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>Windows ファイル

|プロパティ  |説明  |
|---------|---------|
|Enabled     | 設定が適用されるかどうかを決定します。        |
|Item Name     | 追跡するファイルのフレンドリ名。        |
|グループ     | ファイルを論理的にグループ化するためのグループ名。        |
|パスの入力     | ファイル確認のためのパス。例: "c:\temp\myfile.txt"

### <a name="linux-files"></a>Linux ファイル

|プロパティ  |説明  |
|---------|---------|
|Enabled     | 設定が適用されるかどうかを決定します。        |
|Item Name     | 追跡するファイルのフレンドリ名。        |
|グループ     | ファイルを論理的にグループ化するためのグループ名。        |
|パスの入力     | ファイル確認のためのパス (例: "/etc/*.conf")。       |
|パスの種類     | 追跡する項目の種類。"ファイル" または "ディレクトリ" を指定できます。        |
|再帰     | 追跡する項目を検索するときに、再帰を使用するかどうかを決定します。        |
|sudo の使用     | この設定により、項目を確認するときに、sudo を使用するかどうかが決まります。         |
|リンク     | この設定により、ディレクトリを走査するときの、シンボリック リンクの処理方法が決まります。<br> **無視** - シンボリック リンクを無視し、参照先のファイル/ディレクトリを含めません。<br>**フォロー** - 再帰中、シンボリック リンクをフォローします。参照先のファイル/ディレクトリも含めます。<br>**管理** - シンボリック リンクをフォローします。また、返された内容の処理を変更することができます。      |

## <a name="manage-machine-groups"></a>コンピューター グループを管理する

インベントリを使用すると、Azure Monitor ログ内で、マシン グループを作成し、表示することができます。 マシン グループは、Azure Monitor ログ内でクエリによって定義されるマシンのコレクションです。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

コンピューター グループを表示するには、[インベントリ] ページの **[Machine groups] (コンピューター グループ)** タブを選択します。

![[インベントリ] ページでコンピューター グループを表示する](./media/automation-vm-inventory/inventory-machine-groups.png)

一覧からコンピューター グループを選択すると、[Machine groups] (コンピューター グループ) ページが開きます。 このページには、コンピューター グループに関する詳細が表示されます。 これらの詳細には、グループを定義するために使用されるログ分析クエリが含まれます。 ページの下部には、そのグループの一部になっているコンピューターのページ単位の一覧があります。

![コンピューター グループのページを表示する](./media/automation-vm-inventory/machine-group-page.png)

**[+ 複製]** ボタンをクリックして、コンピューター グループを複製します。 ここで、グループに新しい名前とグループの別名を付ける必要があります。 この時点では、定義の変更が可能です。 クエリを変更した後、**[Validate query]\(クエリの検証)** を押して、選択されるコンピューターをプレビューします。 そのグループでよければ、**[作成]** をクリックしてコンピューター グループを作成します

新しいマシン グループを作成する場合は、**[+ マシン グループの作成]** を選択します。 このボタンによって、**[Create a machine group] (コンピューター グループの作成)** ページが開きます。 **[作成]** をクリックしてグループを作成します。

![新しいコンピューター グループを作成する](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>仮想マシンを管理から切断する

インベントリ管理から仮想マシンを除外するには:

1. Azure Portal の左側にある **[Log Analytics]** をクリックし、仮想マシンのオンボード時に使用したワークスペースを選択します。
2. **[Log Analytics]** ウィンドウの **[リソース]** メニューの **[ワークスペースのデータ ソース]** カテゴリで、**[仮想マシン]** を選択します。
3. 切断する仮想マシンを一覧で選択します。 仮想マシンの **[OMS 接続]** 列の **[このワークスペース]** の横に、緑色のチェック マークが表示されます。

   >[!NOTE]
   >OMS は、Azure Monitor ログと呼ばれるようになりました。
   
4. 次のページの上部にある **[切断]** を選択します。
5. 確認ウィンドウで **[はい]** を選択します。
    この操作によって、マシンが管理から切断されます。

## <a name="next-steps"></a>次の手順

* 仮想マシン上のファイルとレジストリ設定内の変更を管理する方法については、「[変更の追跡ソリューションを使用してユーザーの環境内のソフトウェアの変更を追跡する](../log-analytics/log-analytics-change-tracking.md)」を参照してください。
* 仮想マシン上の Windows とパッケージの更新を管理する方法については、「[Azure の Update Management ソリューション](../operations-management-suite/oms-solution-update-management.md)」をご覧ください。

