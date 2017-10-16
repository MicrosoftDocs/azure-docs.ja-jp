---
title: "Microsoft Azure Stack のトラブルシューティング | Microsoft Docs"
description: "Azure Stack のトラブルシューティング。"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 3b40a657ee8eb391d14a38cb95acc0729a8dda21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Microsoft Azure Stack のトラブルシューティング

*適用対象: Azure Stack 開発キット*

このドキュメントでは、Azure Stack の一般的なトラブルシューティング情報を提供します。 

Azure Stack Technical Development Kit は評価環境として提供されているため、Microsoft カスタマー サポート サービスによる公式なサポートは提供されません。  記載されていない問題が発生している場合は、[Azure Stack MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)で追加のサポートと情報について確認してください。  

このセクションで説明されている問題のトラブルシューティングの推奨事項は、複数のソースから取得しており、特定の問題を解決できない場合があります。 コード例はそのまま提供されており、期待される結果を保証できません。 このセクションは、製品の機能強化の実装に合わせて、頻繁に編集および更新されます。

## <a name="deployment"></a>デプロイ
### <a name="deployment-failure"></a>デプロイの失敗
インストール時に障害が発生した場合、デプロイ スクリプトの再実行オプションを使用して、失敗した手順からデプロイを再開できます。  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>デプロイの最後に、PowerShell セッションがまだ開いており、出力が表示されません
この動作は、PowerShell コマンド ウィンドウが選択されている場合の既定の動作の結果に過ぎない可能性があります。 開発キットのデプロイは実際に成功しましたが、ウィンドウを選択したときに、スクリプトが一時停止しました。 これがそのケースであることを確認するには、コマンド ウィンドウのタイトル バーで、"select" という単語を探します。  ESC キーを押してその選択を解除すると、その後に完了メッセージが表示されるはずです。

## <a name="templates"></a>テンプレート
### <a name="azure-template-wont-deploy-to-azure-stack"></a>Azure テンプレートが Azure Stack にデプロイされません
次のことを確認してください。

* テンプレートで、Azure Stack で既に使用できるか、またはプレビュー中である Microsoft Azure サービスを使用している。
* 特定のリソースに使用される API が、ローカル Azure Stack インスタンスでサポートされており、さらに有効な場所 (Azure Stack Development Kit では "ローカル" ですが、Azure では "米国東部" または "インド南部") を対象にしている。
* Azure Resource Manager 構文の小さな違いをキャッチする Test-AzureRmResourceGroupDeployment コマンドレットに関する[この資料](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md)を確認している。

[GitHub リポジトリ](http://aka.ms/AzureStackGitHub/)で既に提供されている Azure Stack テンプレートを使用して、作業の開始に役立てることもできます。

## <a name="virtual-machines"></a>仮想マシン
### <a name="default-image-and-gallery-item"></a>既定のイメージとギャラリー項目
Azure Stack に VM をデプロイする前に、まず Windows Server イメージとギャラリー項目を追加する必要があります。

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Azure Stack ホストの再起動後、一部の VM が自動的に起動しないことがあります。
ホストの再起動後、Azure Stack サービスがすぐに使用できないことに気付く場合があります。  これは、Azure Stack [インフラストラクチャ VM](azure-stack-architecture.md#virtual-machine-roles) および RP が一貫性をチェックするために少し時間がかかるためですが、最終的に自動的に起動します。

また、Azure Stack Development Kit ホストの再起動後に、そのテナント VM が自動的に起動しないことに気付く場合もあります。  これは既知の問題であり、それらをオンラインにするために、いくつかの手動の手順が必要です。

1.  Azure Stack Development Kit ホストで、[スタート] メニューから**フェールオーバー クラスター マネージャー**を起動します。
2.  クラスター **S-Cluster.azurestack.local** を選択します。
3.  **[役割]** を選びます。
4.  テナント VM が*保存済み*状態で表示されます。  すべてのインフラストラクチャ VM が実行されたら、テナント VM を右クリックし、**[開始]** を選択して、VM を再開します。

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>一部の仮想マシンを削除しましたが、まだディスクに VHD ファイルが表示されます。 これは期待される動作ですか。
はい。これは期待される動作です。 これがそのように設計された理由は次のとおりです。

* VM を削除しても VHD は削除されません。 ディスクはリソース グループ内の個別のリソースです。
* ストレージ アカウントが削除されると、削除は Azure Resource Manager (ポータル、PowerShell) にただちに表示されますが、それを格納している可能性のあるディスクは、ガベージ コレクションが実行されるまで、記憶域に維持されます。

"孤立" VHD が表示された場合、それが削除されたストレージ アカウントのフォルダーの一部であるかどうかを知ることが重要です。 ストレージ アカウントが削除されていない場合は、まだ存在していても正常です。

リテンション期間しきい値と、オンデマンドの再利用の設定について詳しくは、[ストレージ アカウントの管理](azure-stack-manage-storage-accounts.md)に関するページを参照してください。

## <a name="storage"></a>ストレージ
### <a name="storage-reclamation"></a>記憶域の再利用
ポータルに再利用された容量が表示されるまで、最大で 14 時間かかる場合があります。 領域の再利用は、ブロック BLOB ストア内の内部コンテナー ファイルの使用率をなど、さまざまな要因に依存します。 そのため、削除されるデータの量によって、ガベージ コレクターの実行時に再利用可能になる領域の量に対する保証はありません。

## <a name="powershell"></a>PowerShell
### <a name="resource-providers-not-registered"></a>リソース プロバイダーが登録されていない
PowerShell を使用してテナント サブスクリプションに接続したときに、リソース プロバイダーが自動的に登録されていないことに気付きます。 [接続モジュール](https://github.com/Azure/AzureStack-Tools/tree/master/Connect)を使用するか、または PowerShell から次のコマンドを実行します (テナントとして[インストールおよび接続した](azure-stack-connect-powershell.md)後に)。 
  
       Get-AzureRMResourceProvider | Register-AzureRmResourceProvider

## <a name="cli"></a>CLI

* CLI 対話モード ( `az interactive`コマンド) はまだ Azure Stack でサポートされていません。
* Azure Stack で使用できる仮想マシン イメージの一覧を取得するには、`az vm image list` コマンドの代わりに、`az vm images list --all` コマンドを使用します。 `--all` オプションを指定すると、応答に、Azure Stack 環境で使用できるイメージのみが返されます。 
* Azure で使用できる仮想マシン イメージの別名は、Azure Stack に適用できない場合があります。 仮想マシン イメージを使用する場合は、イメージの別名の代わりに、URN パラメーター全体 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) を使用する必要がありますします。 さらに、この URN は `az vm images list` コマンドから派生したイメージ仕様と一致している必要があります。
* 既定で、CLI 2.0 は、既定の仮想マシン イメージのサイズとして "Standard_DS1_v2" を使用します。 ただし、このサイズはまだ Azure Stack で使用できないため、仮想マシンを作成するときに `--size` パラメーターを明示的に指定する必要があります。 Azure Stack で使用できる仮想マシンのサイズの一覧を取得するには、`az vm list-sizes --location <locationName>` コマンドを使用します。


## <a name="windows-azure-pack-connector"></a>Windows Azure Pack コネクタ
* Azure Stack Development Kit をデプロイした後に azurestackadmin アカウントのパスワードを変更する場合に、マルチクラウド モードを設定できなくなりました。 そのため、対象となる Windows Azure Pack 環境に接続できません。
* マルチクラウド モードの設定後:
    * ユーザはポータル設定のリセット後にのみ、ダッシュボードを表示できます (ユーザー ポータルで、ポータル設定アイコン (右上隅にある歯車アイコン) をクリックします。 **[既定の設定に戻す]** の下の **[適用]** をクリックします)。
    * ダッシュボードのタイトルが表示されない場合があります。 この問題が発生した場合は、それらを手動で追加する必要があります。
    * 最初にタイルをダッシュボードに追加したときに、一部のタイルが正しく表示されないことがあります。 この問題を解決するには、ブラウザーを更新します。



