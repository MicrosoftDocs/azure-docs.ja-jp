---
title: Azure Lab Services のテンプレート VM で入れ子になった仮想化を有効にする | Microsoft Docs
description: Azure Lab Services のテンプレート VM で入れ子になった仮想化を有効にする方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981935"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Azure Lab Services のテンプレート仮想マシンで入れ子になった仮想化を有効にする
この記事では、Azure Lab Services のテンプレート仮想マシンで入れ子になった仮想化を設定する方法について説明します。 入れ子になった仮想化は、クラス内の各学生に複数のコンピューターが必要な場合に Azure Lab Services で使用されます。
 
## <a name="considerations"></a>考慮事項
入れ子になった仮想化を使用してラボを設定する前に、次の点を考慮する必要があります。

- 新しいラボを作成するときは、仮想マシン サイズとして **[Medium (Nested Virtualization)]\(中 (入れ子になった仮想化)\)** または **[大]** のサイズを選択します。 これらの仮想マシンのサイズでは、入れ子になった仮想化がサポートされます。 
- ホストとクライアントの両方の仮想マシンに優れたパフォーマンスを提供するサイズを選択します。  仮想化を使用する場合、選択するサイズは、1 台のコンピューターだけでなく、ホストおよび同時に実行する必要があるクライアント コンピューターにも適したものでなければならないことに注意してください。
- クライアント仮想マシンは、Azure 仮想ネットワーク上の DNS サーバーなどの Azure リソースにアクセスすることはできません。
- ホスト仮想マシンには、クライアント コンピューターがインターネットに接続できるようにするためのセットアップが必要です。 
- クライアント仮想マシンは、独立したコンピューターとしてライセンスされます。 Microsoft のオペレーティング システムおよび製品のライセンスの詳細については、[Microsoft のライセンス](https://www.microsoft.com/licensing/default)に関するページを参照してください。 テンプレート マシンを設定する前に、使用している他のソフトウェアのライセンス契約を確認してください。

## <a name="enable-nested-virtualization-on-a-template-vm"></a>テンプレート VM で入れ子になった仮想化を有効にする
このセクションの手順では、Windows Server 2016 または Windows Server 2019 の入れ子になった仮想化の設定に焦点を当てます。 スクリプトを使用して、Hyper-V でテンプレート マシンを設定します。 自動化されたソリューションについては、[Lab Services Hyper-V スクリプト](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)のページにあるスクリプトを参照してください。 次の手順で、このスクリプトを使用する方法を示します。

1. Internet Explorer を使用している場合は、信頼済みサイトの一覧に `https://github.com` を追加することが必要になる場合があります。 
    1. Internet Explorer を起動します。
    1. 歯車アイコンを選択し、 **[インターネット オプション]** を選択します。  
    1. **[インターネット オプション]** ダイアログが表示されたら、 **[セキュリティ]** を選択し、 **[信頼済みサイト]** を選択して、 **[サイト]** ボタンをクリックします。
    1. **[信頼済みサイト]** ダイアログが表示されたら、`https://github.com` を信頼済み Web サイトの一覧に追加し、 **[閉じる]** を選択します。

        ![信頼済みサイト](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  次の手順の説明に従って、Git リポジトリ ファイルをダウンロードします。  または、Git リポジトリを [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) から複製することもできます。 
    1. [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) に移動します。
    1. **[Clone or Download]\(複製またはダウンロード\)** ボタンをクリックします。
    1. **[Download ZIP]\(ZIP のダウンロード\)** をクリックします。
    1. ZIP ファイルを解凍します
1. **管理者**モードで **PowerShell** を起動します。
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

## <a name="next-steps"></a>次の手順 
次の記事を参照してください。

- [Azure VM で入れ子になった仮想化を有効にする方法](../../virtual-machines/windows/nested-virtualization.md) 
- [Windows Server に Hyper-V のロールをインストールする](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [Lab Services の Hyper-V スクリプト](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)
