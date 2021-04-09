---
title: Azure Lab Services のテンプレート VM で入れ子になった仮想化を有効にする (スクリプト) | Microsoft Docs
description: スクリプトを使用して、内部に複数の VM を持つテンプレート VM を作成する方法について説明します。  つまり、Azure Lab Services のテンプレート VM で入れ子になった仮想化を有効にします。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5ae50bd11ab9a8adb769920f6d473a2ff2ce9342
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91251497"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>スクリプトを使用して Azure Lab Services のテンプレート仮想マシンで入れ子になった仮想化を有効にする

入れ子になった仮想化を使用すると、ラボのテンプレート仮想マシン内にマルチ VM 環境を作成できます。 テンプレートを公開すると、ラボ内の各ユーザーに、内部に複数の VM が設定された仮想マシンが提供されます。  入れ子になった仮想化と Azure Lab Services の詳細については、「[Azure Lab Services のテンプレート仮想マシンで入れ子になった仮想化を有効にする](how-to-enable-nested-virtualization-template-vm.md)」を参照してください。

この記事の手順では、WindowsServer 2016、Windows Server 2019、または Windows 10 の入れ子になった仮想化の設定に焦点を当てます。 スクリプトを使用して、Hyper-V でテンプレート マシンを設定します。  次の手順では、[Lab Services の Hyper-V スクリプト](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)を使用する方法について説明します。

>[!IMPORTANT]
>ラボの作成時、仮想マシン サイズとして **[Large (Nested Virtualization)]\(大 (入れ子になった仮想化)\)** または **[Medium (Nested Virtualization)]\(中 (入れ子になった仮想化)\)** のサイズを選択します。  それ以外の場合、入れ子になった仮想化は機能しません。  

## <a name="run-script"></a>スクリプトの実行

1. Internet Explorer を使用している場合は、信頼済みサイトの一覧に `https://github.com` を追加することが必要になる場合があります。
    1. Internet Explorer を開きます。
    1. 歯車アイコンを選択し、 **[インターネット オプション]** を選択します。  
    1. **[インターネット オプション]** ダイアログが表示されたら、 **[セキュリティ]** を選択し、 **[信頼済みサイト]** を選択して、 **[サイト]** ボタンをクリックします。
    1. **[信頼済みサイト]** ダイアログが表示されたら、`https://github.com` を信頼済み Web サイトの一覧に追加し、 **[閉じる]** を選択します。

        ![信頼済みサイト](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. 次の手順の説明に従って、Git リポジトリ ファイルをダウンロードします。
    1. [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) に移動します。
    1. **[Clone or Download]\(複製またはダウンロード\)** ボタンをクリックします。
    1. **[Download ZIP]\(ZIP のダウンロード\)** をクリックします。
    1. ZIP ファイルを解凍します

    >[!TIP]
    >Git リポジトリ ([https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)) を複製することもできます。

1. **管理者** モードで **PowerShell** を起動します。
1. PowerShell ウィンドウで、ダウンロードしたスクリプトがあるフォルダーに移動します。 リポジトリ ファイルの先頭のフォルダーから移動する場合、スクリプトは `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` にあります。
1. スクリプトを正常に実行するには、実行ポリシーを変更する必要がある場合があります。 次のコマンドを実行します。

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. 次のスクリプトを実行します。

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > このスクリプトでは、コンピューターの再起動が必要になる場合があります。 スクリプトの指示に従い、 **[Script completed]\(スクリプト完了\)** が出力に表示されるまでスクリプトを再実行してください。
1. 実行ポリシーを忘れずにリセットします。 次のコマンドを実行します。

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>まとめ

これで、テンプレート コンピューターで Hyper-V 仮想マシンを作成する準備ができました。 Hyper-V 仮想マシンの作成方法については、「[Hyper-V で仮想マシンを作成する](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)」を参照してください。 また、[Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) を参照して、使用可能なオペレーティング システムとソフトウェアを確認してください。  

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)