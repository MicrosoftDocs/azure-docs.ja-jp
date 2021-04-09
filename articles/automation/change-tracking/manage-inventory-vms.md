---
title: Azure Automation における VM からのインベントリ収集を管理する | Microsoft Docs
description: この記事では、VM からのインベントリ収集を管理する方法について説明します。
services: automation
ms.subservice: change-inventory-management
keywords: インベントリ, オートメーション, 変更, 追跡
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2acf22ba76acdfa6152ef8966f4000aa325a9e91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92209156"
---
# <a name="manage-inventory-collection-from-vms"></a>VM からのインベントリ収集を管理する

Azure VM のインベントリ追跡は、マシンのリソース ページから有効にすることができます。 ご利用のコンピューター上で次のインベントリ情報を収集して表示できます。

- Windows の更新プログラム、Windows のアプリケーション、サービス、ファイル、レジストリ キー
- Linux ソフトウェア パッケージ、デーモン、ファイル

Azure Automation の変更履歴とインベントリには、インベントリの収集を設定、構成するためのブラウザーベースのユーザー インターフェイスが備わっています。

## <a name="before-you-begin"></a>開始する前に

Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

この記事は、変更履歴とインベントリの対象として有効にする VM があることを前提としています。 Azure VM がない場合は、[VM を作成](../../virtual-machines/windows/quick-create-portal.md)してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>VM リソース ページからインベントリ収集を有効にする

1. Azure Portal の左ウィンドウで **[仮想マシン]** を選択します。
2. VM の一覧で、マシンを選択します。
3. **[リソース]** メニューの **[操作]** で **[インベントリ]** を選択します。
4. データ ログを格納する Log Analytics ワークスペースを選択します。
    そのリージョンで利用可能なワークスペースがない場合は、既定のワークスペースと Automation アカウントを作成するよう求められます。
5. コンピューターを有効にするために、 **[Enable]\(有効化\)** を選択します。

   ![オンボード オプションの表示](./media/manage-inventory-vms/inventory-onboarding-options.png)

    ステータス バーに、変更履歴とインベントリ機能が有効になっていることが通知されます。 このプロセスには最大 15 分かかる可能性があります。 この間、ウィンドウは閉じても開いたままでもかまいません。開いたままにした場合は、機能が有効になると、そのことを伝えるメッセージが表示されます。 デプロイの状態は通知ペインから監視することができます。

   ![インベントリを表示する](./media/manage-inventory-vms/inventory-onboarded.png)

デプロイが完了すると、ステータス バーは表示されなくなります。 システムがまだインベントリ データを収集しているため、データが表示されない可能性があります。 データの完全な収集には 24 時間かかることがあります。

## <a name="configure-your-inventory-settings"></a>インベントリ設定を構成する

既定では、ソフトウェア、Windows サービス、Linux デーモンが収集対象として構成されています。 Windows レジストリとファイル インベントリを収集するには、インベントリ収集設定を構成します。

1. [インベントリ] ページで、ページの上部にある **[設定の編集]** をクリックします。
2. 新しい収集設定を追加するには、 **[Windows レジストリ]** 、 **[Windows ファイル]** 、または **[Linux ファイル]** のタブを選択して、追加する設定カテゴリに移動します。
3. 適切なカテゴリを選択し、ページの上部にある **[追加]** をクリックします。

以降のセクションに、さまざまなカテゴリに対して構成可能な各プロパティの情報を示します。

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
|Enabled     | 設定が適用される場合は True、それ以外の場合は False。        |
|Item Name     | 追跡するファイルのフレンドリ名。        |
|グループ     | ファイルを論理的にグループ化するためのグループ名。       |
|パスの入力     | ファイル確認のためのパス (例: **c:\temp\myfile.txt**)。

### <a name="linux-files"></a>Linux ファイル

|プロパティ  |説明  |
|---------|---------|
|Enabled     | 設定が適用される場合は True、それ以外の場合は False。        |
|Item Name     | 追跡するファイルのフレンドリ名。        |
|グループ     | ファイルを論理的にグループ化するためのグループ名。        |
|パスの入力     | ファイル確認のためのパス (例: **/etc/*.conf**)。       |
|パスの種類     | 追跡する項目の種類です。 値は [ファイル] と [ディレクトリ] です。        |
|再帰     | 追跡する項目を検索するときに、再帰を使用する場合は True、そうでない場合は False。        |
|sudo の使用     | 項目を確認するときに sudo を使用する場合は True、そうでない場合は False。         |
|リンク     | ディレクトリを走査するときの、シンボリック リンクの処理方法を示す値。 次のいずれかの値になります。 <br> 無視 - シンボリック リンクを無視し、参照先のファイルやディレクトリを含めません<br>フォロー - 再帰中、シンボリック リンクに従います。また、参照先のファイルやディレクトリを含めます<br>管理 - シンボリック リンクをフォローします。また、返された内容の処理を変更することができます      |

## <a name="manage-machine-groups"></a>コンピューター グループを管理する

インベントリを使用すると、Azure Monitor ログ内で、マシン グループを作成し、表示することができます。 マシン グループは、Azure Monitor ログ内でクエリによって定義されるマシンのコレクションです。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

コンピューター グループを表示するには、[インベントリ] ページの **[Machine groups] (コンピューター グループ)** タブを選択します。

![[インベントリ] ページでコンピューター グループを表示する](./media/manage-inventory-vms/inventory-machine-groups.png)

一覧からコンピューター グループを選択すると、[Machine groups] (コンピューター グループ) ページが開きます。 このページには、コンピューター グループに関する詳細が表示されます。 これらの詳細には、グループを定義するために使用される Azure Monitor ログ クエリが含まれます。 ページの下部には、そのグループの一部になっているコンピューターのページ単位の一覧があります。

![コンピューター グループのページを表示する](./media/manage-inventory-vms/machine-group-page.png)

**[+ 複製]** をクリックしてマシン グループを複製します。 グループに新しい名前とグループの別名を付ける必要があります。 この時点では、定義の変更が可能です。 クエリを変更した後、 **[クエリの検証]** をクリックして、選択されるマシンをプレビューします。 そのグループでよければ、 **[作成]** をクリックしてマシン グループを作成します。

新しいマシン グループを作成する場合は、 **[+ マシン グループの作成]** をクリックします。 このボタンによって、 **[マシン グループの作成]** ページが開き、そこで新しいグループを定義できます。 **[作成]** をクリックしてグループを作成します。

![新しいコンピューター グループを作成する](./media/manage-inventory-vms/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>VM を管理から切断する

変更履歴とインベントリの管理対象から VM を除外するには、次の手順に従います。

1. Azure portal の左ペインで **[Log Analytics]** を選択し、変更履歴とインベントリの対象として VM を有効にするときに使用したワークスペースを選択します。
2. **[Log Analytics]** ページで、 **[リソース]** メニューを開きます。
3. **[ワークスペースのデータ ソース]** で **[仮想マシン]** を選択します。
4. 切断する VM を一覧から選択します。 マシンの **[OMS 接続]** 列の **[このワークスペース]** の横に、緑色のチェック マークが表示されます。

   >[!NOTE]
   >Operations Management Suite (OMS) は、Azure Monitor ログと呼ばれるようになりました。

5. 次のページの上部にある **[切断]** をクリックします。
6. 確認ウィンドウで **[はい]** をクリックして、マシンを管理から切断します。

>[!NOTE]
>マシンは登録解除された後も表示されます。これは、過去 24 時間以内にインベントリされたすべてのマシンについてレポートするためです。 マシンの接続を切断した後、マシンが一覧に表示されなくなるには、24 時間待機する必要があります。

## <a name="next-steps"></a>次のステップ

* この機能の操作の詳細については、「[Change Tracking と Inventory の管理](manage-change-tracking.md)」を参照してください。
* ソフトウェアの変更の追跡について詳しくは、[変更履歴を使用して環境内のソフトウェアの変更を追跡する](overview.md)方法に関するページを参照してください。
* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](../troubleshoot/change-tracking.md)」を参照してください。
