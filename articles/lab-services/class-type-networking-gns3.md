---
title: Azure Lab Services と GNS3 を使用してネットワーク ラボを設定する | Microsoft Docs
description: GNS3 でネットワークの授業を行うために Azure Lab Services を使用してラボを設定する方法について説明します。
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99499395"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>ネットワーク クラスの授業を行うためのラボを設定する 
この記事では、学生が [GNS3](https://www.gns3.com/) ソフトウェアを使用して仮想および実際のネットワークのエミュレート、構成、テスト、トラブルシューティングを行うことができるように、クラスを設定する方法について説明します。 

この記事は 2 つの主要なセクションから成ります。 最初のセクションでは、クラスルーム ラボを作成する方法について説明します。 2 番目のセクションでは、入れ子になった仮想化を有効にし、GNS3 をインストールおよび構成して、テンプレート マシンを作成する方法について説明します。

## <a name="lab-configuration"></a>ラボの構成
このラボを設定するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成するか、既存のアカウントを使用することができます。 新しいラボ アカウントを作成するには、次のチュートリアルを参照してください: 「[チュートリアル: Azure Lab Services でラボ アカウントを設定する](tutorial-setup-lab-account.md)」。

[こちらのチュートリアル](tutorial-setup-classroom-lab.md)に従い、新しいラボを作成して次の設定を適用します。

| 仮想マシンのサイズ | Image |
| -------------------- | ----- | 
| 大規模 (入れ子になった仮想化) | Windows 10 Pro、バージョン 1909 |

## <a name="template-machine"></a>テンプレート マシン 

テンプレート マシンを作成したら、マシンを起動して接続し、次の 3 つの主要なタスクを実行します。 
 
1. 入れ子になった仮想化用のテンプレート マシンを準備する。
2. GNS3 をインストールする。
3. Hyper-V で入れ子になった GNS3 VM を作成する。
4. Windows Hyper-V VM を使用するように GNS3 を構成する。
5. 適切なアプライアンスを追加する。
6. テンプレートを発行する。


### <a name="prepare-template-machine-for-nested-virtualization"></a>入れ子になった仮想化用のテンプレート マシンを準備する
- [こちらの記事](how-to-enable-nested-virtualization-template-vm.md)の手順に従って、入れ子になった仮想化用のテンプレート仮想マシンを準備します。 

### <a name="install-gns3"></a>GNS3 をインストールする
- [Windows に GNS3 をインストールする](https://docs.gns3.com/docs/getting-started/installation/windows)手順に従います。  次に示すように、コンポーネント ダイアログで **[GNS3 VM]** を必ずインストールに含めてください。

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

最終的には、GNS3 VM の選択になります。 **[Hyper-V]** オプションを必ず選択してください。

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  このオプションを選択すると、PowerShell スクリプトと VHD ファイルがダウンロードされ、Hyper-V マネージャーに GNS3 VM が作成されます。 既定値を使用してインストールを続行します。 **セットアップが完了しても、GNS3 を開始しないでください**。

### <a name="create-gns3-vm"></a>GNS3 VM を作成する
セットアップが完了すると、インストール ファイルと同じフォルダーに ZIP ファイル **"GNS3.VM.Hyper-V.2.2.17.zip"** がダウンロードされます。これには、ドライブと、Hyper-V VM を作成するための PowerShell スクリプトが含まれています。
- GNS3.VM.Hyper-V.2.2.17.zip に対して **[すべて展開]** を行います。  この操作により、ドライブと、VM を作成する PowerShell スクリプトが抽出されます。
- ファイルを右クリックし、"create-vm.ps1" PowerShell スクリプトに対して **[PowerShell で実行]** を行います。
- 実行ポリシーの変更要求が表示される場合があります。 「Y」を入力してスクリプトを実行します。

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- スクリプトが完了したら、Hyper-V マネージャーで VM "GNS3 VM" が作成されたことを確認できます。

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Hyper-V VM を使用するように GNS3 を構成する
GNS3 がインストールされ、GNS3 VM が追加されたので、GNS3 を起動してこの 2 つをリンクします。  [GNS3 セットアップ ウィザードが自動的に起動します](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard)。  
- **[Run appliances from virtual machine]\(仮想マシンからアプライアンスを実行\)** オプションを使用します。  既定値を使用してウィザードの残りの部分を進めていくと、 **[VMware vmrun tool cannot be found]\(VMware vmrun ツールが見つかりません\)** エラーが返されます。

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- **[OK]** を選択し、ウィザードを **キャンセル** して閉じます。
- Hyper-V VM への接続を完了するには、 **[Edit]\(編集\)**  ->  **[Preferences]\(基本設定\)**  ->  **[GNS3 VM]** を開き、 **[Enable the GNS3 VM]\(GNS3 VM を有効にする\)** をオンにして、 **[Hyper-V]** オプションを選択します。
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>適切なアプライアンスを追加する

この時点で、[クラスに適切なアプライアンス](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)を追加する必要があります。

### <a name="publish-template"></a>テンプレートを発行する

テンプレート VM が適切に設定され、発行できるようになりました。ここで、確認する必要がある重要なポイントがいくつかあります。
- GNS3 VM がシャットダウンされていること、または電源がオフになっていることを確認します。  VM がまだ実行されている間に発行すると、VM が破損します。
- GNS3 を閉じます。実行中に発行すると、意図しない副作用が発生する可能性があります。
- インストール ファイルまたはその他の不要なファイルをクリーンアップします。

## <a name="cost"></a>コスト  

このラボのコストを見積もるには、次の例を使用できます。 
 
20 時間の授業が予定されていて、宿題または課題のために 10 時間のクォータが用意されている、25 人の学生のクラスの場合、ラボの価格は次のようになります: 

学生 25 人 * (20 + 10) 時間 * 84 ラボ ユニット * 0.01 USD/時間 = 630 USD 

**重要:** コストの見積もりはあくまでも例です。  価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

## <a name="conclusion"></a>まとめ
この記事では、GNS3 を使用したネットワーク構成用のラボを作成する手順について説明しました。

## <a name="next-steps"></a>次のステップ
次の手順は、どのラボの設定でも同じです。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)。