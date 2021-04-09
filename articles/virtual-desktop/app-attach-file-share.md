---
title: Windows Virtual Desktop での MSIX アプリのアタッチ (プレビュー) のファイル共有の設定 - Azure
description: Windows Virtual Desktop の MSIX アプリのアタッチ用にファイル共有を設定する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 49a350b77958901aae5e54e82d856e4f3772702e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930788"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>MSIX アプリのアタッチ (プレビュー) 用にファイル共有を設定する

> [!IMPORTANT]
> 現在、MSIX アプリのアタッチはパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

すべての MSIX イメージは、読み取り専用アクセス許可を持つホスト プール内のユーザーがアクセスできるネットワーク共有上に格納する必要があります。

MSIX アプリのアタッチ (プレビュー) は、ファイル共有が使用する記憶域ファブリックの種類には依存していません。 MSIX アプリのアタッチ共有に関する考慮事項は、FSLogix 共有のものと同じです。 ストレージの要件の詳細については、「[Windows Virtual Desktop の FSLogix プロファイル コンテナーのストレージ オプション](store-fslogix-profile.md)」をご覧ください。

## <a name="performance-requirements"></a>パフォーマンス要件

システムに応じた MSIX アプリのアタッチ イメージ サイズの限度は、VHD または VHDx ファイルの格納に使用しているストレージの種類と、VHD、VHSD または CIM ファイルとファイル システムのサイズ限度によって異なります。

次の表に、内部に 1 つの MSIX アプリを含む単一の 1 GB MSIX イメージが VM ごとに必要とするリソース数に関する例を示します。

| リソース             | 必要条件 |
|----------------------|--------------|
| 安定状態での IOPS    | 1 IOPS       |
| マシンのブート サインイン | 10 IOPS      |
| 待機時間              | 400 ミリ秒       |

要件は、MSIX イメージに格納されている MSIX パッケージ アプリケーションの数によって大きく異なる場合があります。 大きな MSIX イメージには、より多くの帯域幅を割り当てる必要があります。

### <a name="storage-recommendations"></a>記憶域の推奨事項

Azure には、MSIX アプリのアタッチに使用できる複数のストレージ オプションが用意されています。 Azure Files または Azure NetApp Files を使用することをお勧めします。これらのオプションは、コストと管理のオーバーヘッドの間で最適な価値をもたらすためです。 記事「[Windows Virtual Desktop の FSLogix プロファイル コンテナーのストレージ オプション](store-fslogix-profile.md)」では、Windows Virtual Desktop のコンテキストで Azure で提供されているさまざまなマネージド ストレージ ソリューションが比較されています。

### <a name="optimize-msix-app-attach-performance"></a>MSIX アプリのアタッチのパフォーマンスを最適化する

MSIX アプリのアタッチのパフォーマンスを最適化するためにお勧めのその他の操作を次に示します。

- MSIX アプリのアタッチに使用するストレージ ソリューションは、セッション ホストと同じデータセンターの場所に存在する必要があります。
- パフォーマンスのボトルネックを回避するには、次の VHD、VHDX、および CIM ファイルをウイルス対策スキャンから除外します。
   
    - <MSIXAppAttachFileShare\>\*.VHD
    - <MSIXAppAttachFileShare\>\*.VHDX
    - \\\\storageaccount.file.core.windows.net\\share\*\*.VHD
    - \\\\storageaccount.file.core.windows.net\\share\*\*.VHDX
    - <MSIXAppAttachFileShare>.CIM
    - \\\\storageaccount.file.core.windows.net\\share\*\*.CIM

- MSIX アプリのアタッチ用の記憶域ファブリックを FSLogix プロファイル コンテナーから分離します。
- ファイル共有にアクセスするには、すべての VM システム アカウントとユーザー アカウントに読み取り専用アクセス許可が必要です。
- Windows Virtual Desktop のどのディザスター リカバリー プランにも、セカンダリ フェールオーバーの場所での MSIX アプリのアタッチ ファイル共有のレプリケートが含まれている必要があります。 ディザスター リカバリーの詳細については、「[事業継続とディザスター リカバリー プランを設定する](disaster-recovery.md)」をご覧ください。

## <a name="how-to-set-up-the-file-share"></a>ファイル共有を設定する方法

MSIX アプリのアタッチ ファイル共有の設定プロセスは、[FSLogix プロファイル ファイル共有の設定プロセス](create-host-pools-user-profile.md)とほぼ同じです。 ただし、ユーザーには異なるアクセス許可を割り当てる必要があります。 ファイル共有にアクセスするには、MSIX アプリのアタッチに、読み取り専用アクセス許可が必要です。

MSIX アプリケーションを Azure Files に格納している場合は、セッション ホストに対して、ストレージ アカウントのロールベースのアクセス制御 (RBAC) とファイル共有の New Technology File System (NTFS) の共有に関するアクセス許可の両方を、すべてのセッションホスト VM に割り当てる必要があります。

| Azure オブジェクト                      | 必要なロール                                     | ロール関数                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| セッション ホスト (VM コンピューター オブジェクト)| 記憶域ファイル データの SMB 共有の共同作成者          | 読み取りと実行、読み取り、フォルダー内容の一覧表示  |
| ファイル共有上の管理者              | 記憶域ファイル データの SMB 共有の管理者特権共同作成者 | フル コントロール                                  |
| ファイル共有上のユーザー               | 記憶域ファイル データの SMB 共有の共同作成者          | 読み取りと実行、読み取り、フォルダー内容の一覧表示  |

セッション ホスト VM にストレージ アカウントおよびファイル共有のアクセス許可を割り当てるには:

1. Active Directory Domain Services (AD DS) セキュリティ グループを作成します。

2. すべてのセッション ホスト VM のコンピューター アカウントをグループのメンバーとして追加します。

3. AD DS グループを Azure Active Directory (Azure AD) に同期します。

4. ストレージ アカウントを作成します。

5. 「[Azure ファイル共有を作成する](../storage/files/storage-how-to-create-file-share.md#create-file-share)」の手順に従って、ストレージ アカウントにファイル共有を作成します。

6. 「[パート 1: Azure ファイル共有に対する AD DS 認証を有効にする](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)」の手順に従って、ストレージ アカウントを AD DS に参加させます。

7. 同期された AD DS グループを Azure AD に割り当て、ストレージ アカウントに記憶域ファイル データの SMB 共有の共同作成者ロールを割り当てます。

8. 「[パート 2: ID に共有レベルのアクセス許可を割り当てる](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)」の手順に従って、任意のセッション ホストにファイル共有をマウントします。

9. ファイル共有に対する NTFS アクセス許可を AD DS グループに付与します。

10. ユーザー アカウントの NTFS アクセス許可を設定します。 VM のアカウントが属している AD DS から供給されるオペレーティング ユニット (OU) が必要です。

ID をストレージに割り当てたら、[次の手順](#next-steps)の記事に記載されている手順に従って、必要な他のアクセス許可を VM に割り当てた ID に付与します。

また、セッション ホスト VM に New Technology File System (NTFS) のアクセス許可が付与されていることを確認する必要もあります。 これらのアクセス許可を使用するには、Active Directory Domain Services (AD DS) から供給される操作単位コンテナーが必要です。また、ユーザーはこの操作単位のメンバーである必要があります。

## <a name="next-steps"></a>次のステップ

ファイル共有を設定した後に行う必要があるその他の操作は次のとおりです。

- 「[Azure Files および AD DS を使用してプロファイル コンテナーを作成する](create-file-share.md)」で Azure Files and Azure AD DS を設定する方法を確認します。
- 「[Azure Files および Azure AD DS を使用してプロファイル コンテナーを作成する](create-profile-container-adds.md)」で Azure Files と Azure AD DS を設定する方法を確認します。
- 「[Azure NetApp Files および AD DS を使用してプロファイル コンテナーを作成する](create-fslogix-profile-container.md)」で Azure NetApp Files for MSIX アプリのアタッチを設定する方法を確認します。
- 「[ファイル共有を使用してホスト プール用のプロファイル コンテナーを作成する](create-host-pools-user-profile.md)」で仮想マシンベースのファイル共有を使用する方法を確認します。

完了すると、次のような他のリソースが役に立つことがあります。

- [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) にアクセスして、コミュニティにこの機能に関する質問をします。
- また、Windows Virtual Desktop についてのフィードバックは、[Windows Virtual Desktop フィードバック ハブ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)にお寄せいただくこともできます。
- [MSIX アプリのアタッチの用語集](app-attach-glossary.md)
- [MSIX アプリ アタッチに関するごよくある質問](app-attach-faq.md)
