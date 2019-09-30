---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 59adee2f1d6a99a0a984b9b63c7201266b6381d4
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69984546"
---
Azure Portal または SMB を使用して Azure ファイル共有に加えられた変更は、サーバー エンドポイントに対する変更とは異なり、検出とレプリケーションが即座に行われることはありません。 Azure Files にはまだ変更通知/ジャーナルがないため、ファイルが変更されたときに自動的に同期セッションを開始する方法はありません。 Windows Server では、Azure File Sync は [Windows USN ジャーナル](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx)を使用して、ファイルが変更されたときに同期セッションを自動的に開始します。

Azure ファイル共有に対する変更を検出するために、Azure File Sync には、*変更検出ジョブ*と呼ばれるスケジュールされたジョブがあります。 変更検出ジョブは、ファイル共有内のすべてのファイルを列挙した後、ファイルの同期バージョンと比較します。 変更検出ジョブによってファイルが変更されていると判断された場合に、Azure File Sync は同期セッションを開始します。 変更検出ジョブは 24 時間ごとに実行されます。 変更検出ジョブは Azure ファイル共有内のすべてのファイルを列挙することで機能するため、変更の検出は、大きな名前空間のほうが小さな名前空間よりも時間がかかります。 大規模な名前空間の場合、変更されたファイルを判断するのに 24 時間ごとに 1 回より長くなる場合があります。

Azure ファイル共有で変更されたファイルを直ちに同期したければ、**Invoke-AzStorageSyncChangeDetection** PowerShell コマンドレットを使用すると、Azure ファイル共有における変更の検出を手動で開始できます。 このコマンドレットは、なんらかの自動プロセスによって Azure ファイル共有に変更が加えられたり、管理者によって (ファイルやディレクトリを共有に移動するなどの) 変更が実行されたりするシナリオを意図したものです。 エンド ユーザーの変更に関しては、Azure File Sync エージェントを IaaS VM にインストールし、エンド ユーザーに IaaS VM 経由でファイル共有にアクセスしてもらうことをお勧めします。 そのようにすることで、すべての変更がすぐに他のエージェントと同期され、Invoke-AzStorageSyncChangeDetection コマンドレットを使う必要はありません。 詳細については、[Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) のドキュメントを参照してください。

>[!NOTE]
>REST を使用して Azure ファイル共有に加えられた変更は、SMB の最終更新時刻を更新するものではなく、同期による変更とは見なされません。

Windows Server 上のボリュームに対する USN のような変更検出を Azure ファイル共有に追加するための調査を行っています。 今後この機能の開発を優先的に進めるために、[Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) で投票をお願いします。
