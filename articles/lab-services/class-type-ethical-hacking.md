---
title: Azure Lab Services でエシカル ハッキング ラボを設定する | Microsoft Docs
description: Azure Lab Services を使用してラボを設定し、エシカル ハッキングを教える方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 97cdf13f39cc73ee7f35fb402229469195f1456c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97616424"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>エシカル ハッキング クラスを教えるためのラボを設定する

この記事では、エシカル ハッキングのフォレンジクス面に焦点を当てたクラスを設定する方法について説明します。 侵入テストは、悪意のある攻撃者が利用する可能性のある脆弱性を実証するためにシステムまたはネットワークへの侵入を試みる訓練で、エシカル ハッキング コミュニティで使用します。

エシカル ハッキング クラスの学生は、脆弱性に対する防御のための最新の手法を学習できます。 各学生は、2 つの入れ子になった仮想マシンを持つ Windows Server ホスト仮想マシンを用意し、一方の仮想マシンは [Metasploitable3](https://github.com/rapid7/metasploitable3) イメージ、もう一方のマシンは [Kali Linux](https://www.kali.org/) イメージを使用します。 Metasploitable 仮想マシンは悪用目的に使用し、Kali 仮想マシンでは、フォレンジック タスクの実行に必要なツールを利用できます。

この記事は 2 つの主要なセクションから成ります。 最初のセクションでは、クラスルーム ラボを作成する方法について説明します。 2 番目のセクションでは、入れ子になった仮想化を有効にし、必要なツールとイメージを使用して、テンプレート マシンを作成する方法について説明します。 ここでは、Metasploitable イメージと Kali Linux イメージを使用するマシンで、Hyper-V を有効にして、イメージをホストします。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成するか、既存のアカウントを使用することができます。 新しいラボ アカウントを作成するには、次のチュートリアルを参照してください: 「[チュートリアル: Azure Lab Services でラボ アカウントを設定する](tutorial-setup-lab-account.md)」。

[こちらのチュートリアル](tutorial-setup-classroom-lab.md)に従い、新しいラボを作成して次の設定を適用します。

| 仮想マシンのサイズ | Image |
| -------------------- | ----- |
| 中 (入れ子になった仮想化) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>テンプレート マシン

テンプレート マシンを作成したら、マシンを起動して接続し、次の 3 つの主要なタスクを実行します。

1. 入れ子になった仮想化用のマシンを設定します。 Hyper-V などのすべての適切な Windows 機能を有効にして、相互と通信インターネット接続ができるように Hyper-V イメージのネットワークを設定します。
2. [Kali](https://www.kali.org/) Linux イメージを設定します。 Kali は、侵入テストとセキュリティ監査用のツールが含まれる Linux ディストリビューションです。
3. Metasploitable イメージを設定します。 この例では、[Metasploitable3](https://github.com/rapid7/metasploitable3) イメージを使用します。 このイメージは、故意にセキュリティの脆弱性があるように作成されています。

この記事の残りの部分では、上記のタスクを手動で実行する手順について説明します。  または、[Lab Services の Hyper-V スクリプト](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)と [Lab Services のエシカル ハッキング スクリプト](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)を実行することもできます。

### <a name="prepare-template-machine-for-nested-virtualization"></a>入れ子になった仮想化用のテンプレート マシンを準備する

[入れ子になった仮想化を有効にする](how-to-enable-nested-virtualization-template-vm.md)手順に従って、入れ子になった仮想化用のテンプレート仮想マシンを準備します。

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Kali Linux イメージを使用して入れ子になった仮想マシンを設定する

Kali は、侵入テストとセキュリティ監査用のツールが含まれる Linux ディストリビューションです。

1. [不快感を与えるセキュリティ Kali Linux VM イメージ](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)からイメージをダウンロードします。  ダウンロード ページに記載されている既定のユーザー名とパスワードを覚えておいてください。
    1. VMware の **Kali Linux VMware 64-Bit (7z)** イメージをダウンロードします。
    1. .7z ファイルを抽出します。  7-Zip をまだお持ちでない場合は、[https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) からダウンロードします。 後で必要になるため、抽出したフォルダーの場所を覚えておいてください。
1. 抽出された vmdk ファイルを vhdx ファイルに変換し、Hyper-V で vhdx ファイルを使用できるようにします。 VMware イメージを Hyper-V イメージに変換できるツールがいくつかあります。  ここでは、[StarWind V2V Converter](https://www.starwindsoftware.com/starwind-v2v-converter) を使用します。  ダウンロードするには、[StarWind V2V Converter のダウンロード ページ](https://www.starwindsoftware.com/starwind-v2v-converter#download)を参照してください。
    1. **StarWind V2V Converter** を開始します。
    1. **[Select location of image to convert]\(変換するイメージの場所の選択\)** ページで、 **[Local file]\(ローカル ファイル\)** を選択します。  **[次へ]** を選択します。
    1. **[Source image]\(ソース イメージ\)** ページの **[File name]\(ファイル名\)** の設定では、前のステップで抽出した Kali Linux vmdk ファイルを探して選択します。  ファイルの形式は、Kali-Linux-{version}-vmware-amd64.vmdk です。  **[次へ]** を選択します。
    1. **[Select location of destination image]\(ターゲット イメージの場所の選択\)** で、 **[Local file]\(ローカル ファイル\)** を選択します。  **[次へ]** を選択します。
    1. **[Select destination image format]\(ターゲット イメージの形式の選択\)** ページで、 **[VHD/VHDX]** を選択します。  **[次へ]** を選択します。
    1. **[Select option for VHD/VHDX image format]\(VHD/VHDX イメージ形式のオプションの選択\)** ページで、 **[VHDX growable image]\(VHDX 拡張可能イメージ\)** を選択します。  **[次へ]** を選択します。
    1. **[Select destination file name]\(ターゲット ファイル名の選択\)** ページで、既定のファイル名をそのまま使用します。  **[変換]** を選択します。
    1. **[Converting]\(変換\)** ページで、イメージが変換されるまで待ちます。  この処理には数分かかることがあります。  変換が完了したら、 **[Finish]\(完了\)** を選択します。
1. 新しい Hyper-V 仮想マシンを作成します。
    1. **Hyper-V マネージャー** を開きます。
    1. **[アクション]**  ->  **[新規]**  ->  **[仮想マシン]** を選択します。
    1. **仮想マシンの新規作成ウィザード** の **[開始する前に]** ページで、 **[次へ]** を選択します。
    1. **[名前と場所の指定]** ページで、 **[名前]** に「**Kali-Linux**」と入力し、 **[次へ]** を選択します。
    1. **[世代の指定]** ページで、既定値をそのまま使用し、**[次へ]** を選択します。
    1. **[メモリの割り当て]** ページで、 **[起動メモリ]** に「**2048 MB**」と入力し、 **[次へ]** を選択します。
    1. **[ネットワークの構成]** ページで、接続を **[未接続]** のままにします。 ネットワークアダプターは、後で設定します。
    1. **[仮想ハード ディスクの接続]** ページで、**[既存の仮想ハード ディスクを使用する]** を選択します。 前の手順で作成した **Kali-Linux-{version}-vmware-amd64.vmdk** ファイルの場所を参照し、 **[次へ]** を選択します。
    1. **[仮想マシンの新規作成ウィザードの完了]** ページで、**[完了]** を選択します。
    1. 作成した仮想マシンを Hyper-V マネージャーで選択します。 マシンはまだ起動しないでください。  
    1. **[アクション]**  ->  **[設定]** を選択します。
    1. **[Kali-Linux の設定]** ダイアログで、 **[ハードウェアの追加]** を選択します。
    1. **[レガシ ネットワーク アダプター]** を選択し、**[追加]** を選択します。
    1. **[レガシ ネットワーク アダプター]** ページで、**[仮想スイッチ]** 設定の **LabServicesSwitch** を選択し、**[OK]** を選択します。 LabServicesSwitch は、「**入れ子になった仮想化用のテンプレート マシンを準備する**」セクションの Hyper-V 用のテンプレート マシンを準備するときに作成されました。
    1. これで、Kali-Linux イメージを使用できるようになりました。 **Hyper-V マネージャー** から、**[アクション]** -> **[開始]** を選択し、**[アクション]** -> **[接続]** を選択して仮想マシンに接続します。  既定のユーザー名は **kali**、パスワードは **kali** です。

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Metasploitable イメージを使用して入れ子になった VM を設定する  

Rapid7 Metasploitable イメージは、故意にセキュリティの脆弱性があるように構成されているイメージです。 このイメージを使用して、問題をテストして検出します。 次の手順では、作成済みの Metasploitable イメージを使用する方法について説明します。 ただし、新しいバージョンの Metasploitable イメージが必要な場合は、[https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) を参照してください。

1. Metasploitable イメージをダウンロードします。
    1. [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html) に移動します。 フォームに入力してイメージをダウンロードし、**[Submit]\(送信\)** ボタンを選択します。
    2. **[Download Metasploitable Now]\(Metasploitable を今すぐダウンロード\)** ボタンを選択します。
    3. ZIP ファイルをダウンロードしたら、ZIP ファイルを抽出し、Metasploitable.vmdk の場所を記録しておきます。
1. 抽出された vmdk ファイルを vhdx ファイルに変換し、Hyper-V で vhdx ファイルを使用できるようにします。 VMware イメージを Hyper-V イメージに変換できるツールがいくつかあります。  ここでは、[StarWind V2V Converter](https://www.starwindsoftware.com/starwind-v2v-converter) を再び使用します。  ダウンロードするには、[StarWind V2V Converter のダウンロード ページ](https://www.starwindsoftware.com/starwind-v2v-converter#download)を参照してください。
    1. **StarWind V2V Converter** を開始します。
    1. **[Select location of image to convert]\(変換するイメージの場所の選択\)** ページで、 **[Local file]\(ローカル ファイル\)** を選択します。  **[次へ]** を選択します。
    1. **[Source image]\(ソース イメージ\)** ページの **[File name]\(ファイル名\)** の設定では、前のステップで抽出した Metasploitable.vmdk を探して選択します。  **[次へ]** を選択します。
    1. **[Select location of destination image]\(ターゲット イメージの場所の選択\)** で、 **[Local file]\(ローカル ファイル\)** を選択します。  **[次へ]** を選択します。
    1. **[Select destination image format]\(ターゲット イメージの形式の選択\)** ページで、 **[VHD/VHDX]** を選択します。  **[次へ]** を選択します。
    1. **[Select option for VHD/VHDX image format]\(VHD/VHDX イメージ形式のオプションの選択\)** ページで、 **[VHDX growable image]\(VHDX 拡張可能イメージ\)** を選択します。  **[次へ]** を選択します。
    1. **[Select destination file name]\(ターゲット ファイル名の選択\)** ページで、既定のファイル名をそのまま使用します。  **[変換]** を選択します。
    1. **[Converting]\(変換\)** ページで、イメージが変換されるまで待ちます。  この処理には数分かかることがあります。  変換が完了したら、 **[Finish]\(完了\)** を選択します。
1. 新しい Hyper-V 仮想マシンを作成します。
    1. **Hyper-V マネージャー** を開きます。
    1. **[アクション]**  ->  **[新規]**  ->  **[仮想マシン]** を選択します。
    1. **仮想マシンの新規作成ウィザード** の **[開始する前に]** ページで、 **[次へ]** を選択します。
    1. **[名前と場所の指定]** ページで、**[名前]** に **Metasploitable** を入力し、**[次へ]** を選択します。

        ![新しい VM イメージ ウィザード](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. **[世代の指定]** ページで、既定値をそのまま使用し、**[次へ]** を選択します。
    1. **[メモリの割り当て]** ページで、**[起動メモリ]** に **512 MB** を入力し、**[次へ]** を選択します。

        ![[メモリの割り当て] ページ](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. **[ネットワークの構成]** ページで、接続を **[未接続]** のままにします。 ネットワークアダプターは、後で設定します。
    1. **[仮想ハード ディスクの接続]** ページで、**[既存の仮想ハード ディスクを使用する]** を選択します。 前の手順で作成した **metasploitable.vhdx** ファイルの場所を参照し、**[次へ]** を選択します。

        ![[仮想ネットワーク ディスクの接続] ページ](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. **[仮想マシンの新規作成ウィザードの完了]** ページで、**[完了]** を選択します。
    1. 作成した仮想マシンを Hyper-V マネージャーで選択します。 マシンはまだ起動しないでください。  
    1. **[アクション]**  ->  **[設定]** を選択します。
    1. **[Metasploitable の設定]** ダイアログで、**[ハードウェアの追加]** を選択します。
    1. **[レガシ ネットワーク アダプター]** を選択し、**[追加]** を選択します。

        ![ネットワーク アダプター ページ](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. **[レガシ ネットワーク アダプター]** ページで、**[仮想スイッチ]** 設定の **LabServicesSwitch** を選択し、**[OK]** を選択します。 LabServicesSwitch は、「**入れ子になった仮想化用のテンプレート マシンを準備する**」セクションの Hyper-V 用のテンプレート マシンを準備するときに作成されました。

        ![[レガシ ネットワーク アダプター] ページ](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. これで、Metasploitable イメージを使用できるようになりました。 **Hyper-V マネージャー** から、**[アクション]** -> **[開始]** を選択し、**[アクション]** -> **[接続]** を選択して仮想マシンに接続します。  既定のユーザー名は **msfadmin**、パスワードは **msfadmin** です。

テンプレートが更新され、エシカル ハッキング侵入テスト クラスに必要なイメージ、侵入テストを実行するツールがあるイメージ、検出対象のセキュリティの脆弱性がある別のイメージが含まれるようになりました。 テンプレート イメージをクラスに発行できるようになりました。 テンプレート ページの **[発行]** ボタンを選択して、テンプレートをラボに発行します。

## <a name="cost"></a>コスト  

このラボのコストを見積もるには、次の例を使用できます。

20 時間の授業が予定されていて、宿題または課題のために 10 時間のクォータが用意されている、25 人の学生のクラスの場合、ラボの価格は次のようになります:

学生 25 人 \* (20 + 10) 時間 \* 55 ラボ ユニット \* 0.01 USD/時間 = 412.50 USD

>[!IMPORTANT]
>コストの見積もりはあくまでも例です。 価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

## <a name="conclusion"></a>まとめ

この記事では、エシカル ハッキング クラスのラボを作成する手順について説明しました。 侵入テストを行えるように、入れ子になった仮想化を設定して、ホスト仮想マシン内に 2 つの仮想マシンを作成する手順が含まれます。

## <a name="next-steps"></a>次のステップ

次の手順は、どのラボの設定でも同じです。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)。
