---
title: "Azure ファイル同期のトラブルシューティング (プレビュー) | Microsoft Docs"
description: "Azure ファイル同期における一般的な問題のトラブルシューティング"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: cf3f3cf63cafc3b883d26144a53066ee421eb2a6
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshoot-azure-file-sync-preview"></a>Azure ファイル同期のトラブルシューティング (プレビュー)
Azure ファイル同期 (プレビュー) を使用すると、オンプレミスまたは Azure の Windows Server に共有をレプリケートできます。 管理者とユーザーは、SMB や NFS 共有などを使って Windows Server 経由でファイル共有にアクセスします。 この方法は、ブランチ オフィスなどの Azure データ センターから離れた場所にあるデータにアクセスして変更する場合に特に便利です。 複数のブランチ オフィス間など、複数の Windows Server エンドポイント間でデータをレプリケートできます。

この記事は、Azure ファイル同期のデプロイで発生した問題のトラブルシューティングと解決を支援するように設計されています。 解決しない場合、このガイドでは、問題をさらに調査するのに役立つ重要なログをシステムから収集する方法を示します。 Azure ファイル同期についてサポートを受けるには次のオプションを使用できます。

- Microsoft サポート: 新しいサポート ケースを作成するには、Azure Portal の [ヘルプとサポート] タブに移動し、[新しいサポート要求] をクリックします。
- [Azure Storage フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazuredata)

## <a name="how-to-troubleshoot-agent-installation-failures"></a>エージェントのインストール エラーのトラブルシューティングを行う方法
Azure ファイル同期エージェントのインストールに失敗する場合は、管理者特権でのコマンド プロンプトから次のコマンドを実行してエージェントのインストール時のログ記録を有効にします。

```
StorageSyncAgent.msi /l*v Installer.log
```

インストールに失敗した後で、installer.log をレビューして原因を特定します。

## <a name="server-is-not-listed-under-registered-servers-in-the-azure-portal"></a>Azure Portal の [登録済みサーバー] にサーバーが表示されない
サーバーがストレージ同期サービスの [登録済みサーバー] に表示されない場合は、次の手順を実行します。
1. 登録するサーバーにログインします。
2. ファイル エクスプローラーを開き、ストレージ同期エージェントのインストール ディレクトリ (既定の場所は `C:\Program Files\Azure\StorageSyncAgent`) に移動します。 
3. ServerRegistration.exe を実行し、ウィザードに従ってストレージ同期サービスにサーバーを登録します。

## <a name="server-registration-displays-the-following-message-after-installing-the-azure-file-sync-agent-this-server-is-already-registered"></a>Azure ファイル同期エージェントのインストール後、[サーバーの登録] に、"This server is already registered" (このサーバーは既に登録されています) というメッセージが表示される
!["server is already registered" (このサーバーは既に登録されています) エラー メッセージが表示された [サーバーの登録] ダイアログのスクリーンショット](media/storage-sync-files-troubleshoot/server-registration-1.png)

このメッセージは、以前にサーバーがストレージ同期サービスに登録された場合に表示されます。 現在のストレージ同期サービスでサーバーを登録解除し、新しいストレージ同期サービスに登録するには、「[Unregister a server with Azure File Sync (Azure ファイル同期でのサーバーの登録解除)](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)」の手順に従います。

## <a name="how-to-troubleshoot-sync-not-working-on-a-server"></a>サーバーで同期が機能しない場合のトラブルシューティングを行う方法
サーバーで同期が失敗する場合は、次の手順を実行します。
- Azure Portal で Azure ファイル共有と同期するディレクトリのサーバー エンドポイントが存在することを確認します。
    
    ![Azure Portal にクラウドとサーバー両方のエンドポイントが表示されている同期グループのスクリーンショット](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- `Applications and Services\Microsoft\FileSync\Agent` にあるイベント ビューアーで、操作および診断イベント ログをレビューします。
- サーバーにインターネット接続があることを確認します。
- サービス MMC スナップインを開いて、サーバーで実行されている Azure ファイル同期サービスを確認し、ストレージ同期エージェント サービス (FileSyncSvc) が実行されていることを確認します。

## <a name="how-to-troubleshoot-individual-files-failing-to-sync"></a>同期に失敗する個々のファイルのトラブルシューティングを行う方法 
個々のファイルが同期に失敗する場合は、次の手順を実行します。
- イベント ビューアーで、`Applications and Services\Microsoft\FileSync\Agent` にある操作および診断イベント ログをレビューします
- ファイルに開いているハンドルがないことを確認します
    - 注: Azure ファイル同期は、開いているハンドルがあるファイルを同期するために VSS スナップショットを定期的に取得します

## <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>階層化に失敗するファイルのトラブルシューティングを行う方法
ファイルが Azure Files への階層化に失敗する場合は、次の手順を実行します。

- ファイルが Azure ファイル共有に存在することを確認します
    - 注: 階層化の前に、ファイルが Azure ファイル共有と同期されている必要があります
- イベント ビューアーで、`Applications and Services\Microsoft\FileSync\Agent` にある操作および診断イベント ログをレビューします
- サーバーにインターネット接続があることを確認します 
- Azure ファイル同期フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します
    - 管理者特権でのコマンド プロンプトを開いて `fltmc` を実行し、StorageSync.sys と StorageSyncGuard.sys のファイル システム フィルター ドライバーが表示されることを確認します

## <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>再呼び出しに失敗するファイルのトラブルシューティングを行う方法
ファイルの再呼び出しが失敗する場合は、次の手順を実行します。
- イベント ビューアーで、`Applications and Services\Microsoft\FileSync\Agent` にある操作および診断イベント ログをレビューします
- ファイルが Azure ファイル共有に存在することを確認します
- サーバーにインターネット接続があることを確認します 
- Azure ファイル同期フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します
    - 管理者特権でのコマンド プロンプトを開いて `fltmc` を実行し、StorageSync.sys と StorageSyncGuard.sys のファイル システム フィルター ドライバーが表示されることを確認します

## <a name="how-to-troubleshoot-files-being-unexpectedly-recalled-on-a-server"></a>サーバーで予期せず再呼び出しされるファイルのトラブルシューティングを行う方法
ウイルス対策、バックアップ、および多数のファイルを読み取るその他のアプリケーションは、オフライン属性を尊重し、それらのファイルのコンテンツの読み取りをスキップする場合を除き、望ましくない再呼び出しの原因となります。 サポート対象のこれらの製品のオフライン ファイルをスキップすると、ウイルス対策スキャンやバックアップ ジョブなどの操作を実行するときに、望ましくない呼び出しを回避できます。

オフライン ファイルの読み取りをスキップするようにソリューションを構成する方法については、ソフトウェア ベンダーにお問い合わせください。

ファイル エクスプローラーでのファイルの参照のようなその他のシナリオで、望ましくない呼び出しがさらに発生する可能性があります。 サーバーのファイル エクスプローラーでクラウド階層化ファイルを含むフォルダーを開くと、望ましくない再呼び出しが発生する可能性があります。サーバーでウイルス対策が有効になっている場合は、その可能性がさらに高くなります。

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング
サーバーで Azure ファイル同期の問題が発生する場合は、次の手順を実行します。
- イベント ビューアーで診断および操作イベント ログをレビューします
    - 同期、階層化、再呼び出しの問題は、`Applications and Services\Microsoft\FileSync\Agent` にある診断および操作イベント ログに記録されます
    - サーバーの管理 (たとえば、構成設定) に関する問題は、`Applications and Services\Microsoft\FileSync\Management` にある診断および操作イベント ログに記録されます
- Azure ファイル同期サービスがサーバーで実行されていることを確認します
    - サービス MMC スナップインを開き、ストレージ同期エージェント サービス (FileSyncSvc) が実行されていることを確認します
- Azure ファイル同期フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します
    - 管理者特権でのコマンド プロンプトを開いて fltmc を実行し、StorageSync.sys と StorageSyncGuard.sys のファイル システム フィルター ドライバーが表示されることを確認します

上記の手順を実行しても問題が解決しない場合は、次の手順を実行して AFSDiag ツールを実行します。
1. AFSDiag 出力を保存するために使用するディレクトリを作成します (例: c:\output)。
2. 管理者特権で PowerShell ウィンドウを開き、次のコマンドを実行します (各コマンドの後で Enter キーを押します)。

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. Azure ファイル同期のカーネル モード トレース レベルには、(より詳細なトレースの作成を指定しない限り) 1 を入力し、Enter キーを押します。
4. Azure ファイル同期のユーザー モード トレース レベルには、(より詳細なトレースの作成を指定しない限り) 1 を入力し、Enter キーを押します。
5. 問題を再現させ、終了したら D キーを押します。
6. ログ ファイルとトレース ファイルを含む .zip ファイルが指定した出力ディレクトリに作成されます。
