---
title: Azure Import/Export ジョブの状態を表示する | Microsoft Docs
description: Import/Export ジョブの状態および使用されているドライブの状態を表示する方法を説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 225164fe00f70839446f8b74155cd3959f745a49
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467749"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Azure Import/Export ジョブの状態を表示する

この記事では、Azure Import/Export ジョブのドライブとジョブの状態を表示する方法について説明します。 Azure Import/Export サービスは、Azure BLOB と Azure Files に大量のデータを安全に転送するために使用されます。 このサービスは、Azure BLOB Storage からデータをエクスポートするためにも使われます。  

## <a name="view-job-and-drive-status"></a>ジョブとドライブの状態を表示する
Azure Portal から、インポート ジョブまたはエクスポート ジョブの状態を追跡できます。 **[インポート/エクスポート]** タブをクリックします。ジョブの一覧がページに表示されます。

![ジョブの状態の追跡](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>ジョブの状態を見る

ドライブの処理状況に応じて、次のジョブの状態のいずれかが表示されます。

| ジョブの状態 | 説明 |
|:--- |:--- |
| 作成 | ジョブが作成された後、ジョブの状態は**Creating** (作成) に設定されます。 ジョブが **Creating** 状態である間、Import/Export サービスはドライブがデータ センターにまだ配送されていないものと見なします。 ジョブはこの状態に最大 2 週間留まることができ、その後はサービスによって自動的に削除されます。 |
| 発送 | パッケージを発送したら、Azure Portal で追跡情報を更新する必要があります。  これにより、ジョブは **Shipping** (発送) 状態になります。 ジョブは、**Shipping** 状態に最大 2 週間留まります。 
| 受取済み | すべてのドライブがデータ センターで受け取られると、ジョブの状態は **Received** (受取済み) に設定されます。 |
| 転送 | 少なくとも 1 つのドライブの処理が開始されると、ジョブの状態は **Transferring** (転送) に設定されます。 詳しくは、「[ドライブの状態を表示する](#view-drive-status)」をご覧ください。 |
| 梱包 | すべてのドライブの処理が完了してから、ドライブがユーザーに返送されるまでの間、ジョブの状態は**梱包**に設定されます。 |
| 完了 | すべてのドライブがユーザーに返送され、ジョブがエラーなく完了すると、ジョブは **Completed** (完了) に設定されます。 **Completed** 状態になってから 90 日が経過すると、ジョブは自動的に削除されます。 |
| クローズ | すべてのドライブがユーザーに返送された後、ジョブの処理中に何らかのエラーがあった場合、ジョブは**Closed** (クローズ) に設定されます。 **Closed** 状態になってから 90 日が経過すると、ジョブは自動的に削除されます。 |

## <a name="view-drive-status"></a>ドライブの状態を表示する

次の表は、各ドライブがインポート ジョブまたはエクスポート ジョブを通じて処理されるまでのライフ サイクルを示しています。 ジョブに含まれる各ドライブの現在の状態は、Azure portal に表示されます。

次の表では、ジョブ内の各ドライブの状態がたどる経過について説明しています。

| ドライブの状態 | 説明 |
|:--- |:--- |
| 指定済み | インポート ジョブの場合、Azure portal からジョブが作成されたときのドライブの初期状態は **Specified** (指定済み) です。 エクスポート ジョブの場合、ジョブの作成時にドライブは指定されないので、ドライブの初期状態は**Received** (受取済み) です。 |
| 受取済み | Import/Export サービスがインポート ジョブの配送業者から受け取ったドライブを処理すると、ドライブの状態は**Received** (受取済み) に変わります。 エクスポート ジョブの場合は、**Received** (受取済み) がドライブの初期状態になります。 |
| NeverReceived | ジョブのパッケージが到着したものの、パッケージにドライブが含まれていなかった場合は、ドライブの状態は **NeverReceived** (未着) になります。 また、出荷情報を受信してから 2 週間経過してもパッケージがデータ センターに到着しない場合にも、このドライブ状態が設定されます。 |
| 転送 | ドライブから Azure Storage へのデータ転送が開始されると、ドライブの状態は**Transferring** (転送) に変わります。 |
| 完了 | すべてのデータがエラーなく正常に転送されると、ドライブの状態は**Completed** (完了) に変わります。
| CompletedMoreInfo | ドライブとストレージ間でのデータ コピー中に何らかの問題が発生した場合は、ドライブの状態は **CompletedMoreInfo** (完了、情報あり) に変わります。 この情報には、エラー、警告、または BLOB の上書きに関する情報メッセージが含まれる場合があります。
| ShippedBack | データ センターから返送先に向けてドライブが発送されると、ドライブの状態は **ShippedBack** (返送済み) に変わります。 |

Azure Portal の次の画像では、サンプル ジョブのドライブの状態が表示されています。

![ドライブの状態の表示](./media/storage-import-export-service/drivestate.png)

次の表は、ドライブのエラー状態と、各状態に対して実行されるアクションを示したものです。

| ドライブの状態 | Event | 解決方法/次の手順 |
|:--- |:--- |:--- |
| NeverReceived | **NeverReceived** としてマークされたドライブ (ジョブの出荷プロセスを通じて受け取られなかったドライブ) は、別便で配送されます。 | 運用チームはドライブを **Received** にします。 |
| 該当なし | ジョブの対象でないドライブは、別のジョブを通じてデータ センターに配送されます。 | これらのドライブは追加ドライブとしてマークされ、元のパッケージに関連付けられたジョブが完了したときに、ユーザーに返送されます。 |

## <a name="time-to-process-job"></a>ジョブの処理の所要時間
インポート/エクスポート ジョブの処理にかかる時間は、次のような多くの要因によって異なります。

-  発送時刻
-  データ センターでの負荷
-  ジョブの種類とコピーされているデータのサイズ
-  ジョブに含まれるディスクの数 

Import/Export サービスに SLA はありませんが、ディスクの到着後 7 日から 10 日以内にコピーが完了するよう努力しております。 Azure Portal に掲載される状態に加え、REST API を使用してジョブの進行状況を追跡できます。 [List Jobs](/previous-versions/azure/dn529083(v=azure.100)) 操作 API 呼び出しの達成率パラメーターでは、コピーの進捗率が示されます。


## <a name="next-steps"></a>次の手順

* [WAImportExport ツールを設定する](storage-import-export-tool-how-to.md)
* [AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy.md)
* [Azure Import Export REST API サンプル](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
