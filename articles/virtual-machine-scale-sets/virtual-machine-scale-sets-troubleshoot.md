---
title: 仮想マシン スケール セットの自動スケールに関するトラブルシューティング
description: 仮想マシン スケール セットの自動スケールに関するトラブルシューティングを行います。 よくある問題とその解決方法について説明します。
author: avirishuv
ms.author: avverma
ms.topic: troubleshooting
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviwer: jushiman
ms.custom: avverma
ms.openlocfilehash: 11302c301bee466f678d544d0c4838c39cec9c8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91818534"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>仮想マシン スケール セットの自動スケールに関するトラブルシューティング
**問題** – Azure Resource Manager で仮想マシン スケール セットを使って自動スケール インフラストラクチャを作成しました。このとき、 https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale のようなテンプレートをデプロイしました。定義したスケール規則は正常に機能しましたが、仮想マシンの負荷をいくら増やしても、自動スケールが実行されません。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次のような点を検討します。

* 各 VM の vCPU 数はいくつですか。すべての vCPU を読み込んでいますか。
  上の例の Azure クイックスタート テンプレートには do_work.php スクリプトが含まれ、1 つの vCPU が読み込まれます。 Standard_A1 や D1 などの単一 vCPU の VM よりも大きいサイズの VM を使用する場合は、この負荷を複数回読み込む必要があります。 VM の vCPU 数を確認する方法については、「[Azure の Windows 仮想マシンのサイズ](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
* 仮想マシン スケール セット内の VM 数はいくつですか。VM ごとに設定を行っていませんか。
  
    自動スケール規則で定義された時間内に、スケール セット内の **すべての** VM における平均 CPU 使用率がしきい値を超えた場合にのみ、スケールアウト イベントが発生します。
* スケール イベントを見逃していませんか。
  
    Azure ポータルで監査ログを確認し、スケール イベントを探してください。 スケールアップやスケールダウンを見逃している可能性があります。 "Scale" でフィルター処理できます。
  
    ![[監査ログ]][audit]
* スケールインとスケールアウトのしきい値の間に十分な差を確保していますか。
  
    たとえば、平均 CPU 使用率が 5 分間にわたって 50% を超えた場合にスケールアウトし、平均 CPU 使用率が 50% を割り込んだときにスケールインする規則を設定したとします。 この設定は、CPU 使用率がこのしきい値に近付くと、スケール アクションによってセットのサイズが絶え間なく増減される "フラッピング" 問題をよく引き起こします。 この設定が原因で、自動スケール サービスはこの "フラッピング" を防止しようとするため、自動スケールが実行されなくなる場合があります。 そのため、スケールアウトとスケールインのしきい値の間に十分な差を付けて、自動スケーリングが実行される余裕を確保する必要があります。
* 独自の JSON テンプレートを作成しましたか。
  
    JSON テンプレートは間違いが起きやすいため、最初のうちは上の例のような確実に機能するテンプレートを基にして、少しずつ変更を加えるようにしてください。 
* 手動でスケールインまたはスケールアウトできますか。
  
    "capacity" 設定を変えた仮想マシン スケール セット リソースを再デプロイして、VM 数を手動で変更してみてください。 テンプレートの例については、 https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing をご覧ください。必要に応じてテンプレートを編集し、スケール セットで使われている仮想マシン サイズと同じにしてください。 VM 数を手動で変更できた場合、問題の原因が自動スケールに限定されます。
* Microsoft.Compute/virtualMachineScaleSet を確認してください。また、[Azure リソース エクスプローラー](https://resources.azure.com/)で Microsoft.Insights リソースを確認してください。
  
    Azure Resource Explorer は Azure Resource Manager リソースの状態を表示できるため、トラブルシューティングには欠かせません。 サブスクリプションをクリックし、トラブルシューティングを行うリソース グループを表示します。 Compute リソース プロバイダーの下で、作成した仮想マシン スケール セットを探し、インスタンス ビューでデプロイの状態を確認します。 また、仮想マシン スケール セット内の VM のインスタンス ビューも確認します。 次に Microsoft.Insights リソース プロバイダーに移動し、自動スケール規則が適切かどうかを確認します。
* 診断拡張機能が動作し、パフォーマンス データを出力していますか。
  
    **更新:** Azure の自動スケーリングはホストベースのメトリック パイプラインを使用するように強化されており、診断拡張機能のインストールは必須ではなくなりました。 つまり、新しいパイプラインを使用する自動スケール アプリケーションを作成する場合、この後のいくつかの段落の説明はもう適用されません。 ホスト パイプラインを使用するように変換された Azure テンプレートの例は、 https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale にあります。 
  
    次の理由により、自動スケールにはホストベースのメトリックを使用することをお勧めします。
  
  * 診断拡張機能をインストールする必要がないことによる変動要素の減少。
  * テンプレートの単純化。 既存のスケール セット テンプレートに Insights の自動スケール ルールを追加するだけです。
  * 信頼性の高いレポートと新しい VM の高速起動。
    
    診断拡張機能を引き続き使用する唯一の理由は、メモリ診断レポート/スケーリングが必要であるかどうかです。 ホストベースのメトリックでは、メモリに関するレポートは行われません。
    
    以上を念頭を置いたうえで引き続き診断拡張機能を自動スケールで使用する場合のみ、この記事の残りの説明を参照してください。
    
    Azure Resource Manager の自動スケールは、診断拡張機能と呼ばれる VM 拡張機能を使用して動作できます (ただし、現在はその機能の使用は必須ではありません)。 テンプレートで定義されているストレージ アカウントにパフォーマンス データが出力されます。 その後、このデータは Azure Monitor サービスによって集計されます。
    
    Insights サービスは、VM からデータを読み取れなかった場合、ユーザーにメールを送信することになっています。 たとえば VM がダウンしている場合、メールが届きます。 Azure アカウントを作成するときに指定したメール アドレスに届いたメールを必ずチェックしてください。
    
    自分でデータを確認することもできます。 Cloud Explorer を使用して Azure ストレージ アカウントを確認します。 たとえば [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2) を使用して、現在利用中の Azure サブスクリプションにログインして選択します。 次に、デプロイ テンプレートの診断拡張機能の定義で参照されている診断ストレージ アカウントの名前に注目します。
    
    ![Cloud Explorer][explorer]
    
    各 VM のデータが格納される一連のテーブルが表示されています。 例として、Linux と CPU のメトリックで最新の行を見てみましょう。 Visual Studio Cloud Explorer はクエリ言語をサポートしているので、自分でクエリを実行することができます。 たとえば、「Timestamp gt datetime’2016-02-02T21:20:00Z’」のようなクエリを実行することで最新のイベントを確実に取得できます。 タイムゾーンは UTC に対応します。 ここに表示されるデータは、設定したスケール規則に対応しているでしょうか。 次の例では、仮想マシン 20 の CPU 使用率がこの 5 分間で 100% に上昇しています。
    
    ![Storage Tables][tables]
    
    データがない場合、VM で実行されている診断拡張機能に問題があることがわかります。 データが存在する場合は、スケール規則と Insights サービスのどちらかに問題があることがわかります。 [Azure の状態](https://azure.microsoft.com/status/)を確認してください。
    
    これまでの手順を終えても、自動スケールに関する問題が解決しない場合は、次のリソースをご利用ください。 
    * [Microsoft Q&A 質問ページ](/answers/topics/azure-virtual-machines.html)または [Stack overflow](https://stackoverflow.com/questions/tagged/azure) でフォーラムを読む 
    * サポートに問い合わせる。 テンプレートと、パフォーマンス データのビューを共有できるように準備しておいてください。

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
