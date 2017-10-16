---
title: "Azure へのフェールオーバー障害のトラブルシューティング | Microsoft Docs"
description: "この記事では、Azure へのフェールオーバー時の一般的なエラーをトラブルシューティングする方法を説明します。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: pratshar
ms.openlocfilehash: 0e50433e1ccdcbc0010070eec110914f0d33b5ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>仮想マシンから Azure へのフェールオーバー時のエラーをトラブルシューティングする
仮想マシンから Azure へフェールオーバーを実行中に、次のいずれかのエラーを受け取る可能性があります。 トラブルシューティングするには、各エラー条件に説明されている手順に従います。


## <a name="failover-failed-with-error-id-28031"></a>エラー ID 28031 でフェールオーバーが失敗している

Site Recovery は、フェールオーバーした仮想マシンを Azure に作成できませんでした。 次のような原因が考えられます。

* 仮想マシンの作成に使用できる十分なクォータがありません。[サブスクリプション] > [使用量 + クォータ] の順に移動して、使用可能なクォータを確認できます。 [新しいサポート要求](http://aka.ms/getazuresupport)を開いて、クォータを増やすことができます。
     
* 同じ可用性セットにある異なるサイズ ファミリの仮想マシンのフェールオーバーを試行しています。 同じ可用性セットにあるすべての仮想マシンに対しては、必ず同じサイズ ファミリを選択してください。 仮想マシンの [コンピューティングとネットワーク] の設定に移動してサイズを変更し、フェールオーバーを再試行してください。
  
* サブスクリプションに、仮想マシンの作成を禁止しているポリシーがあります。 仮想マシンの作成を許可するようにポリシーを変更して、フェールオーバーを再試行してください。 

## <a name="failover-failed-with-error-id-28092"></a>エラー ID 28092 でフェールオーバーが失敗している

Site Recovery は、フェールオーバーされた仮想マシンのネットワーク インターフェイスを作成できませんでした。 サブスクリプションでネットワーク インターフェイスを作成するために使用できる十分なクォータがあることを確認してください。 [サブスクリプション] > [使用量 + クォータ] の順に移動して、使用可能なクォータを確認できます。 [新しいサポート要求](http://aka.ms/getazuresupport)を開いて、クォータを増やすことができます。 十分なクォータがある場合、この現象は一時的に発生している可能性があります。もう一度、操作をやり直してください。 再試行しても問題が続く場合、この文書の最後にコメントを残してください。  

## <a name="failover-failed-with-error-id-70038"></a>エラー ID 70038 でフェールオーバーが失敗している

Site Recovery は、フェールオーバーした従来の仮想マシンを Azure に作成できませんでした。 次のような原因が考えられます。

* 仮想ネットワークなど、仮想マシンの作成に必要ないずれかのリソースが存在しない。 仮想マシンの [コンピューティングとネットワーク] の設定に指定されているように仮想ネットワークを作成するか、既に存在している仮想ネットワークの設定を変更して、フェールオーバーを再試行してください。 


## <a name="next-steps"></a>次のステップ

さらにサポートが必要な場合は、[Site Recovery フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)にクエリを投稿するか、この文書の最後にコメントを残してください。 ユーザー支援を可能にするために必要なアクティブ コミュニティを設置しています。