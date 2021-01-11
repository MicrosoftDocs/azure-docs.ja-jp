---
title: Azure Lab Services での VM の自動シャットダウンを構成する
description: この記事では、ラボ アカウントでの VM の自動シャットダウンを構成する方法について説明します。
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8647aed0e66993b8a7b8e5c0a42c8ceabbb1fb9e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798450"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>ラボ アカウント用に VM の自動シャットダウンを構成する

自動シャットダウンによる複数のコスト制御機能を有効にして、仮想マシンがアクティブに使用されていないときの追加コストを事前に防ぐことができます。 次の 3 つの自動シャットダウンおよび切断機能を組み合わせると、ユーザーが誤って仮想マシンを実行状態のまま放置するケースのほとんどを捕捉できます。
 
- OS がアイドル状態と見なした仮想マシンからユーザーを自動的に切断する (Windows のみ)。
- ユーザーが切断したときに仮想マシンを自動的にシャットダウンする (Windows および Linux)。
- 起動したがユーザーが接続しない仮想マシンを自動的にシャットダウンする。

自動シャットダウン機能の詳細については、[自動シャットダウン設定を使用したコスト制御の最大化](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)に関するセクションを参照してください。

## <a name="enable-automatic-shutdown"></a>自動シャットダウン処理を有効にする

1. [Azure portal](https://portal.azure.com/) で、 **[ラボ アカウント]** ページに移動します。
1. 左側のメニューで **[ラボ設定]** を選択します。
1. シナリオに適した自動シャットダウン設定を選択します。  

    > [!div class="mx-imgBorder"]
    > ![ラボ アカウントの自動シャットダウン設定](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    ここでの設定は、ラボ アカウントで作成されたすべてのラボに適用されます。 ラボ作成者 (教師) は、ラボ レベルでこの設定をオーバーライドできます。 ラボ アカウントでこの設定を変更した場合、変更後に作成されたラボにのみ変更が適用されます。

    設定を無効にするには、このページのチェック ボックスをオフにします。 

## <a name="next-steps"></a>次のステップ

ラボ所有者がこの設定をラボ レベルで構成またはオーバーライドする方法については、[ラボの VM の自動シャットダウンの構成](how-to-enable-shutdown-disconnect.md)に関するページを参照してください
