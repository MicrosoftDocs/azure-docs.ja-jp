---
title: "Azure ファイル同期をデプロイする方法 (プレビュー) | Microsoft Docs"
description: "Azure ファイル同期をデプロイする方法を開始から終了まで説明します。"
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
ms.openlocfilehash: d9cf205cd3e7a8017efbe9078ff495c04f065374
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="how-to-deploy-azure-file-sync-preview"></a>Azure ファイル同期をデプロイする方法 (プレビュー)
Azure ファイル同期 (プレビュー) を使用すると、オンプレミスまたは Azure 内で共有をレプリケートできます。また、Windows Server 上で SMB または NFS を介して共有にアクセスすることができます。 Azure ファイル同期は、ブランチ オフィスなどの Azure データ センターから離れた場所にあるデータにアクセスして変更する必要がある場合に便利です。 複数のブランチ オフィス間など、複数の Windows Server エンドポイント間でデータをレプリケートできます。

このガイドの手順に従う前に、「[Azure Files のデプロイの計画](storage-files-planning.md)」と「[Azure ファイル同期のデプロイの計画](storage-sync-files-planning.md)」を読むことを強くお勧めします。

## <a name="prerequisites"></a>前提条件
* Azure ファイル同期をデプロイするのと同じリージョンにストレージ アカウントと Azure ファイル共有があること。詳細については、次を参照してください。
    - Azure ファイル同期の[リージョンの可用性](storage-sync-files-planning.md#region-availability)に関するページ
    - ストレージ アカウントの詳しい作成手順については、[ストレージ アカウントの作成](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関するページ
    - ファイル共有の詳しい作成手順については、[ファイル共有の作成](storage-how-to-create-file-share.md)に関するページ。
* Azure ファイル同期と同期する少なくとも 1 つのサポートされる Windows Server または Windows Server クラスター。サポートされている Windows Server バージョンの詳細については、[Windows Server との相互運用性](storage-sync-files-planning.md#azure-file-sync-interoperability)に関するページをご覧ください。

## <a name="deploy-the-storage-sync-service"></a>ストレージ同期サービスのデプロイ 
ストレージ同期サービスは、Azure ファイル同期を表す最上位レベルの Azure リソースです。ストレージ同期サービスをデプロイするには、[Azure Portal](https://portal.azure.com/) に移動し、Azure ファイル同期を検索します。検索結果から [Azure File Sync (preview)]\(Azure ファイル同期 (プレビュー)\) を選択した後、[作成] を選択して [ストレージ同期のデプロイ] タブを開きます。

結果のブレードで、次の情報を求められます。

- **名前**: ストレージ同期サービスの (サブスクリプションごとに) 一意の名前。
- **サブスクリプション**: ストレージ同期サービスを作成するサブスクリプション。 組織の構成方針によっては、1 つ以上のサブスクリプションにアクセスできる場合があります。 Azure サブスクリプションは、各クラウド サービス (Azure Files など) に対する課金の最も基本的なコンテナーです。
- **リソース グループ**: リソース グループは、ストレージ アカウントやストレージの同期サービスなどの Azure リソースの論理グループです。 新しいリソース グループを作成するか、Azure ファイル同期の既存のリソース グループを使用できます (HR リソースや特定のプロジェクトのリソースのグループ化など、組織のリソースを論理的に分離するために使用するコンテナーとしてリソース グループを使用することをお勧めします)。
- **場所**: Azure ファイル同期をデプロイするリージョン。この一覧ではサポートされているリージョンのみを使用できます。

[ストレージ同期のデプロイ] フォームに入力したら、[作成] をクリックしてストレージ同期サービスをデプロイします。

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>Azure ファイル同期で使用する Windows Server の準備
フェールオーバー クラスターのサーバー ノードを含め、Azure ファイル同期を使用するすべてのサーバーに対して、次の手順を実行します。

フェールオーバー クラスターのサーバー ノードを含め、Azure ファイル同期で使用するすべてのサーバーに対して、次の手順を実行します。

1. Internet Explorer セキュリティ強化の構成を無効にします。 これは、サーバーの初期登録のみに必要で、サーバーの登録後にもう一度有効にすることができます。
    1. サーバー マネージャーを開きます。
    2. **[ローカル サーバー]** をクリックします。  
        ![サーバー マネージャー UI の左側にある [ローカル サーバー]](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. [プロパティ] サブウィンドウで、**[IE セキュリティ強化の構成]** のリンクを選択します。  
        ![サーバー マネージャーの UI の [IE セキュリティ強化の構成]](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. [Internet Explorer セキュリティ強化の構成] ポップアップ ウィンドウで、[管理者] と [ユーザー] の両方に対して **[オフ]** を選択します。  
        ![[オフ] が選択された [Internet Explorer セキュリティ強化の構成] ポップアップ ウィンドウ](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. PowerShell 5.1.\* 以上を実行していることを確認します(Windows Server 2016 では PowerShell 5.1 が既定です)。 $PSVersionTable オブジェクトの PSVersion プロパティの値を調べて、PowerShell 5.1.\* を実行していることを確認できます。

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - Windows Server 2012 R2 のほとんどのインストールのように、PSVersion が 5.1.\* 未満の場合は、[Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) をダウンロードしてインストールすることで簡単にアップグレードできます。 Windows Server 2012 R2 でダウンロードしてインストールする適切なパッケージは、**Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu** です。

3. [Azure PowerShell をインストールして構成](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)します。 常に最新バージョンの Azure PowerShell モジュールを使用することをお勧めします。

## <a name="install-the-azure-file-sync-agent"></a>Azure ファイル同期エージェントをインストールする
Azure ファイル同期エージェントは、Windows Server を Azure ファイル共有と同期できるようにするダウンロード可能なパッケージです。 エージェントは、[Microsoft ダウンロード センター](https://go.microsoft.com/fwlink/?linkid=858257)からダウンロードできます。 ダウンロードしたら、MSI パッケージをダブルクリックして Azure ファイル同期エージェントのインストールを開始します。

> [!Important]  
> フェールオーバー クラスターで Azure ファイル同期を使用する場合、クラスターのすべてのノードに Azure ファイル同期エージェントをインストールする必要があります。

Azure ファイル同期エージェントのインストール パッケージは、追加のプロンプトがあまり多くないため、比較的簡単にインストールできます。 次の方法をお勧めします。
- 既定のインストール パス (`C:\Program Files\Azure\StorageSyncAgent`) のままにして、トラブルシューティングとサーバーのメンテナンスを簡略化します。
- Microsoft Update を有効にして、Azure ファイル同期を常に最新の状態に保ちます。 機能の更新プログラムと修正プログラムを含め、Azure ファイル同期エージェントに対するすべての更新が Microsoft Update から実行されます。 常に最新の更新プログラムを Azure ファイル同期に適用することをお勧めします。詳細については、[Azure ファイル同期の更新ポリシー](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)に関するページをご覧ください。

Azure ファイル同期エージェントのインストールの最後に、[サーバーの登録] UI が自動的に開始します。 Azure ファイル同期でこのサーバーを登録する方法については、次のセクションをご覧ください。

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server をストレージ同期サービスに登録する
Windows Server をストレージ同期サービスに登録すると、サーバー (またはクラスター) とストレージ同期サービスの間に信頼関係が確立されます。 Azure ファイル同期エージェントのインストール後に [サーバーの登録] UI が自動的に開始するはずですが、開始しない場合は `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` から手動で開くことができます。 [サーバーの登録] UI が表示されたら、**[サインイン]** をクリックして開始します。

サインイン後に、次の情報の入力を求められます。

![[サーバーの登録] UI のスクリーンショット](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure サブスクリプション**: ストレージ同期サービスを含むサブスクリプション (前述の「[ストレージ同期サービスのデプロイ](#deploy-the-storage-sync-service)」をご覧ください)。 
- **リソース グループ**: ストレージ同期サービスを含むリソース グループ。
- **ストレージ同期サービス**: 登録するストレージ同期サービスの名前。

ドロップ ダウンから適切な情報を選択した後で、**[登録]** をクリックしてサーバー登録を完了します。 登録プロセスの一環として、追加のサインインを求められます。

## <a name="create-a-sync-group"></a>同期グループの作成
同期グループは、一連のファイルの同期トポロジを定義します。 同期グループ内のエンドポイントは、相互に同期状態が維持されます。 同期グループには、Azure ファイル共有を表すクラウド エンドポイントが少なくとも 1 つと、Windows サーバー上のパスを表すサーバー エンドポイントが 1 つ含まれている必要があります。 同期グループを作成するには、[Azure Portal](https://portal.azure.com/) でストレージ同期サービスに移動し、**[+ 同期グループ]** をクリックします。

![Azure Portal での新しい同期グループの作成](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

結果のウィンドウでは、クラウド エンドポイントを含む同期グループを作成するための次の情報を求められます。

- **同期グループ名**: 作成する同期グループの名前。 この名前は、ストレージ同期サービス内で一意である必要がありますが、理にかなった任意の名前を指定できます。
- **サブスクリプション**: 前述の「[ストレージ同期サービスのデプロイ記憶域の同期サービスをデプロイ](#deploy-the-storage-sync-service)」でストレージ同期サービスをデプロイしたサブスクリプション。
- **ストレージ アカウント**: [ストレージ アカウントの選択] をクリックすると追加のウィンドウが開き、同期する Azure ファイル共有を含むストレージ アカウントを選択できます。
- **Azure ファイル共有**: 同期する Azure ファイル共有の名前。

サーバー エンドポイントを追加するには、新規に作成した同期グループに移動し、[サーバー エンドポイントの追加] をクリックします。

![[同期グループ] ウィンドウで新しいサーバー エンドポイントを追加する](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

結果として表示される [サーバー エンドポイントの追加] ウィンドウでは、サーバー エンドポイントを作成するための次の情報を要求されます。

- **登録済みサーバー**: サーバー エンドポイントを作成するサーバーまたはクラスターの名前。
- **パス**: 同期グループの一部として同期する Windows Server 上のパス。
- **クラウドの階層化**: クラウドの階層化の有効または無効を切り替えるスイッチです。頻繁には使用またはアクセスされないファイルを Azure Files に階層化することができます。
- **ボリュームの空き領域**: サーバー エンドポイントが配置されているボリュームに確保する空き領域のサイズ。 たとえば、単一のサーバー エンドポイントで [ボリュームの空き領域] をボリュームの 50% に設定すると、データの約半量が Azure Files に階層化されます。 クラウドの階層化が有効かどうかにかかわらず、Azure ファイル共有は、データの完全なコピーを常に同期グループ内に保持します。

[作成] をクリックしてサーバー エンドポイントを追加します。 Azure ファイル共有と Windows Server でファイルの同期が維持されます。 

## <a name="next-steps"></a>次のステップ
- [Azure ファイル同期のサーバー エンドポイントの追加/削除](storage-sync-files-server-endpoint.md)
- [Azure ファイル同期でのサーバーの登録/登録解除](storage-sync-files-server-registration.md)
