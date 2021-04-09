---
title: Azure DevTest Labs の必須成果物を指定する | Microsoft Docs
description: ユーザーが選択した成果物のラボの仮想マシン (VM) へのインストールに先立ってインストールする必要がある必須成果物の指定方法を説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0677f8bea35cb34735fdcf34e717eea349fad8bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480305"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Azure DevTest Labs のラボに対する必須成果物を指定する
ラボ所有者として、ラボ内に作成されるすべてのマシンに適用される必須成果物を指定できます。 ラボ内の各マシンを企業ネットワークに接続してほしいシナリオを想像してください。 この場合、各ラボ ユーザーは、仮想マシンの作成時にドメイン参加成果物を追加して、各自のマシンが企業ドメインに接続されるようにする必要があります。 つまり、ラボ ユーザーが各自のマシンに必須成果物を適用するのを忘れた場合、基本的にはマシンを作成し直す必要があります。 ラボ所有者として、ドメイン参加成果物をラボの必須成果物として作成します。 この手順により、各マシンが企業ネットワークに確実に接続され、ラボ ユーザーの時間と労力が節約されます。
 
その他の必須成果物として、チームが共通して使用するツールや、各マシンが既定で所有する必要があるプラットフォーム関連のセキュリティ パックがあります。つまり、ラボ内のすべてのマシンが必要とする共通ソフトウェアが、必須成果物になります。 必須成果物が適用されるマシンからカスタム イメージを作成し、そのイメージから新しいマシンを作成した場合、マシンの作成中に必須成果物が再度適用されます。 この動作は、カスタム イメージが古い場合でも、そのイメージからマシンを作成するときに、毎回の作成フロー中に必須成果物の更新された最新バージョンが適用されることも意味します。 
 
パラメーターがない成果物のみが、必須成果物としてサポートされます。 ラボ ユーザーは、ラボの作成時に追加のパラメーターを入力する必要はなく、これにより VM の作成プロセスが単純になります。 

## <a name="specify-mandatory-artifacts"></a>必須アーティファクトの指定
Windows マシンと Linux マシン用の必須成果物を個別に選択できます。 適用する順序に応じて、これらの成果物の順序を変更することもできます。 

1. ラボの [ホーム] ページで、**[設定]** の下の **[構成とポリシー]** を選択します。 
3. **[外部リソース]** の下の **[Mandatory artifacts]\(必須成果物\)** を選択します。 
4. **[Windows]** セクションまたは **[Linux]** セクションの **[編集]** を選択します。 この例では、**[Windows]** オプションを使用します。 

    ![[Mandatory artifacts]\(必須成果物\) ページ - [編集] ボタン](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. 成果物を選択します。 この例では、**[7-Zip]** オプションを使用します。 
5. **[成果物の追加]** ページで、**[追加]** を選択します。 

    ![[Mandatory artifacts]\(必須成果物\) ページ - [7-Zip] の追加](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. 別の成果物を追加するには、成果物を選択し、**[追加]** を選択します。 この例では、2 番目の必須成果物として **[Chrome]** を追加します。

    ![[Mandatory artifacts]\(必須成果物\) ページ - [Chrome] の追加](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. **[Mandatory artifacts]\(必須成果物\)**] ページに、選択した成果物の数を示すメッセージが表示されます。 このメッセージをクリックすると、選択した成果物が表示されます。 **[保存]** を選択して変更を保存します。 

    ![[Mandatory artifacts]\(必須成果物\) ページ - 成果物の保存](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Linux VM 用の必須成果物を指定するには、これらの手順を繰り返します。 
    
    ![[Mandatory artifacts]\(必須成果物\) ページ - Windows と Linux の成果物](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. 一覧から成果物を **削除** するには、行の末尾にある **[...]** (省略記号) を選択し、**[削除]** を選択します。 
10. 一覧の成果物を **並べ替える** には、成果物の上にマウスを移動し、行の先頭に表示される **[...]** (省略記号) を選択し、項目を新しい位置にドラッグします。 
11. 必須成果物をラボに保存するには、**[保存]** を選択します。 

    ![[Mandatory artifacts]\(必須成果物\) ページ - ラボへの成果物の保存](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. **[構成とポリシー]** ページを閉じて (右上隅にある **[X]** を選択して)、ラボのホーム ページに戻ります。  

## <a name="delete-a-mandatory-artifact"></a>必須成果物を削除する
ラボから必須成果物を削除するには、次の操作を実行します。 

1. **[設定]** の下の **[構成とポリシー]** を選択します。 
2. **[外部リソース]** の下の **[Mandatory artifacts]\(必須成果物\)** を選択します。 
3. **[Windows]** セクションまたは **[Linux]** セクションの **[編集]** を選択します。 この例では、**[Windows]** オプションを使用します。 
4. 上部にある必須成果物の数を示しているメッセージを選択します。 

    ![[Mandatory artifacts]\(必須成果物\) ページ - メッセージの選択](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. **[選択した成果物]** ページで、削除する成果物の **[...]** (省略記号) を選択し、**[削除]** を選択します。 
    
    ![[Mandatory artifacts]\(必須成果物\) ページ - 成果物の削除](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. **[OK]** を選択して、**[選択した成果物]** ページを閉じます。 
7. **[Mandatory artifacts]\(必須成果物\)** ページで **[保存]** を選択します。
8. 必要に応じて、**Linux** イメージに対してこれらの手順を繰り返します。 
9. **[保存]** を選択して、すべての変更をラボに保存します。 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>VM の作成時に必須成果物を表示する
ここでは、ラボ ユーザーとして、ラボ内に VM を作成するときに、必須成果物の一覧を表示できます。 ラボ所有者によってラボに設定されている必須成果物は、編集することも削除することもできません。

1. ラボの [ホーム] ページで、メニューから **[概要]** を選択します。
2. VM をラボに追加するには、**[+ 追加]** を選択します。 
3. **[基本イメージ]** を選択します。 この例では、**[Windows Server, version 1709]** を使用します。
4. **[成果物]** に、選択された必須成果物の数を示すメッセージが表示されます。 
5. **[アーティファクト]** を選択します。 
6. ラボの構成とポリシーに指定されている **必須成果物** の表示を確認します。 

    ![VM の作成 - 必須成果物](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>次のステップ
* [ラボへの Git アーティファクト リポジトリの追加](devtest-lab-add-artifact-repo.md)方法を学習します。

