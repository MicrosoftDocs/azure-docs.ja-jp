---
title: Azure Lab Services のラボで VM の自動シャットダウンを構成する
description: リモート デスクトップ接続が切断されたときの VM の自動シャットダウンを有効または無効にする方法について説明します。
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 0df3fdcaea99c00461caac37a3b655d152a0e527
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798484"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>ラボで VM の自動シャットダウンを構成する

この記事では、ラボで VM の自動シャットダウンを構成する方法について説明します。

自動シャットダウンによる複数のコスト制御機能を有効にして、仮想マシンがアクティブに使用されていないときの追加コストを事前に防ぐことができます。 次の 3 つの自動シャットダウンおよび切断機能を組み合わせると、ユーザーが誤って仮想マシンを実行状態のまま放置するケースのほとんどをキャッチできます。
 
* OS がアイドル状態と見なした仮想マシンからユーザーを自動的に切断する (Windows のみ)。
* ユーザーが切断したときに仮想マシンを自動的にシャットダウンする (Windows および Linux)。
* 起動したがユーザーが接続しない仮想マシンを自動的にシャットダウンする。

自動シャットダウン機能の詳細については、[自動シャットダウン設定を使用したコスト制御の最大化](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)に関するセクションを参照してください。

ラボ アカウント管理者は、ラボを作成するラボ アカウントにこの設定を構成できます。 詳細については、[ラボ アカウントで VM の自動シャットダウンを構成する](how-to-configure-lab-accounts.md)方法に関するページを参照してください。 ラボ所有者は、ラボの作成時またはラボの作成後に、設定をオーバーライドできます。 

## <a name="configure-for-the-lab-level"></a>ラボ レベル用に構成する

自動シャットダウンの設定は、[Azure Lab Services](https://labs.azure.com/) で構成できます。

* ラボの作成時 (**ラボ ポリシー**から)、または
* ラボの作成後 ( **[設定]** から)

> [!div class="mx-imgBorder"]
> ![ラボの作成時に構成する](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

[自動シャットダウン設定を使用したコスト制御の最大化](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)に関するセクションで、自動シャットダウンの詳細を確認するようにしてください。

> [!WARNING]
> VM に対する RDP セッションを切断する前に VM で Windows オペレーティング システム (OS) をシャットダウンすると、自動シャットダウン機能が正常に機能しなくなります。  

## <a name="next-steps"></a>次のステップ

[クラスルーム ラボ用のダッシュボード](use-dashboard.md)
