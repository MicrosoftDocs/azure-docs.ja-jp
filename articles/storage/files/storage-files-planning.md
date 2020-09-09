---
title: Azure Files のデプロイの計画 | Microsoft Docs
description: Azure Files デプロイの計画について理解します。 Azure File Sync を使用し、Azure ファイル共有を直接マウントするか、オンプレミスでキャッシュできます。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: db7ae0bd33bc52f80788db4994dcf2a3ca4d909a
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705913"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Files のデプロイの計画
[Azure Files](storage-files-introduction.md) は、サーバーレスの Azure ファイル共有を直接マウントすることと、Azure File Sync を使用してオンプレミスで Azure ファイル共有をキャッシュすることの 2 つの主な方法でデプロイできます。選択するデプロイ オプションによって、デプロイを計画する際に考慮する必要がある内容が変わります。 

- **Azure ファイル共有を直接マウントする**:Azure Files では SMB アクセスが提供されるため、Windows、macOS、および Linux で使用可能な標準的な SMB クライアントを使用して、オンプレミスまたはクラウドで Azure ファイル共有をマウントすることができます。 Azure ファイル共有はサーバーレスであるため、運用環境でデプロイするシナリオでは、ファイル サーバーや NAS デバイスを管理する必要ありません。 つまり、ソフトウェアの修正プログラムを適用したり、物理ディスクを交換したりする必要はありません。 

- **Azure File Sync を使用したオンプレミスでの Azure ファイル共有のキャッシュ**:Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持しながら、Azure Files で組織のファイル共有を一元化できます。 Azure File Sync によって、オンプレミス (またはクラウド) の Windows Server が Azure ファイル共有の高速キャッシュに変換されます。 

この記事では主に、オンプレミスまたはクラウド クライアントによって直接マウントされる Azure ファイル共有をデプロイする場合の、デプロイに関する考慮事項について説明します。 Azure File Sync のデプロイを計画する場合は、「[Azure File Sync のデプロイの計画](storage-sync-files-planning.md)」を参照してください。

## <a name="management-concepts"></a>管理の概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Azure ファイル共有をストレージ アカウントにデプロイする場合は、次のことをお勧めします。

- 他の Azure ファイル共有を使用したストレージ アカウントへの Azure ファイル共有のデプロイのみ。 GPv2 ストレージ アカウントでは混在する目的のストレージ アカウントを使用できますが、Azure ファイル共有や BLOB コンテナーなどのストレージ リソースではストレージ アカウントの制限が共有されるため、リソースを混在させると、後でパフォーマンスの問題のトラブルシューティングがより困難になる可能性があります。 

- Azure ファイル共有をデプロイする際には、ストレージ アカウントの IOPS 制限に注意してください。 ファイル共有をストレージ アカウントに 1:1 でマップするのが理想的ですが、これは、組織と Azure の両方からのさまざまな制限や制約により、実現できない場合もあります。 1 つのストレージ アカウントに 1 つのファイル共有のみをデプロイすることができない場合は、使用頻度が高い共有と低い共有を考慮し、最もアクティブなファイル共有が同じストレージ アカウントに一緒にデプロイされないようにしてください。

- GPv2 と FileStorage アカウントのみをデプロイし、ご利用の環境内で GPv1 とクラシック ストレージ アカウントが見つかった場合はそれらをアップグレードします。 

## <a name="identity"></a>ID
Azure ファイル共有にアクセスするには、ファイル共有のユーザーを認証し、共有へのアクセスを承認する必要があります。 これは、ファイル共有にアクセスするユーザーの ID に基づいて行われます。 Azure Files は、次の 3 つの主な ID プロバイダーと統合されています。
- **オンプレミス Active Directory Domain Services (AD DS、またはオンプレミス AD DS)** :Azure ストレージ アカウントは、Windows Server ファイル サーバーや NAS デバイスと同様に、顧客所有の Active Directory Domain Services ドメインに参加させることができます。 ドメイン コントローラーは、Azure VM や、別のクラウド プロバイダーの VM としてもオンプレミスにデプロイできます。Azure Files は、ドメイン コントローラーがホストされている場所に依存しません。 ストレージ アカウントがドメインに参加すると、エンド ユーザーは、PC にサインインしたユーザー アカウントを使用してファイル共有をマウントできます。 AD ベースの認証では、Kerberos 認証プロトコルが使用されます。
- **Azure Active Directory Domain Services (Azure AD DS)** :Azure AD DS では、Azure リソースに使用できる Microsoft が管理するドメイン コントローラーが提供されます。 Azure AD DS ドメインにストレージ アカウントを参加させることで、顧客所有の Active Directory ドメインに参加させる場合と同様の利点が得られます。 このデプロイ オプションは、AD ベースのアクセス許可を必要とするアプリケーションのリフトアンドシフト シナリオに最も役立ちます。 Azure AD DS では AD ベースの認証が提供されるため、このオプションでも Kerberos 認証プロトコルが使用されます。
- **Azure ストレージ アカウント キー**:Azure ファイル共有は、Azure ストレージ アカウント キーを使用してマウントすることもできます。 この方法でファイル共有をマウントするために、ストレージ アカウント名がユーザー名として使用され、ストレージ アカウント キーがパスワードとして使用されます。 ストレージ アカウント キーを使用して Azure ファイル共有をマウントすることは、実質的には管理者の操作です。これは、マウントされたファイル共有には、ACL がある場合でも、共有上のすべてのファイルとフォルダーに対する完全なアクセス許可が付与されるためです。 ストレージ アカウント キーを使用して SMB 経由でマウントする場合は、NTLMv2 認証プロトコルが使用されます。

オンプレミスのファイル サーバーから移行するか、あるいは Windows ファイル サーバーや NAS アプライアンスと同様に動作させることが目的の Azure Files で新しいファイル共有を作成するお客様の場合は、**顧客所有 Active Directory** ドメインにストレージ アカウントを参加させることをお勧めします。 顧客所有 Active Directory ドメインへのストレージ アカウントの参加の詳細については、[Azure Files Active Directory の概要](storage-files-active-directory-overview.md)に関するページを参照してください。

ストレージ アカウント キーを使って Azure ファイル共有にアクセスする場合は、「[ネットワーク](#networking)」セクションの説明に従って、サービス エンドポイントを使用することをお勧めします。

## <a name="networking"></a>ネットワーク
Azure ファイル共有には、ストレージ アカウントのパブリック エンドポイントを介して、どこからでもアクセスできます。 つまり、ユーザーのログオン ID によって承認された要求など、認証済みの要求は、Azure の内外から安全に送信できます。 多くのお客様の環境では、Azure VM からの Azure ファイル共有のマウントは成功しても、オンプレミス ワークステーションでの最初のマウントは失敗します。 その理由は、SMB での通信に使用されるポート (445) が、多くの組織とインターネット サービス プロバイダー (ISP) によってブロックされているためです。 ポート 445 からのアクセスを許可する ISP または許可しない ISP の概要を確認するには、[TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) を参照してください。

Azure ファイル共有へのアクセスのブロックを解除するための次の主な 2 つのオプションがあります。

- 組織のオンプレミス ネットワークに対してポート 445 のブロックを解除します。 Azure ファイル共有には、SMB 3.0 や FileREST API などのインターネットで安全なプロトコルを使用して、パブリック エンドポイント経由でのみ外部からアクセスできます。 これが、オンプレミスから Azure ファイル共有にアクセスする最も簡単な方法です。これは、組織の送信ポート規則の変更のほかに、高度なネットワーク構成が必要ではないためです。しかし、SMB のレガシおよび非推奨バージョンの SMB (つまり、SMB 1.0) を削除することをお勧めします。 これを行う方法については、「[Windows/Windows Server のセキュリティ保護](storage-how-to-use-files-windows.md#securing-windowswindows-server)」と「[Linux のセキュリティ保護](storage-how-to-use-files-linux.md#securing-linux)」を参照してください。

- ExpressRoute または VPN 接続経由で Azure ファイル共有にアクセスします。 ネットワーク トンネル経由で Azure ファイル共有にアクセスする場合、SMB トラフィックが組織の境界を通過しないため、オンプレミスのファイル共有のように Azure ファイル共有をマウントすることができます。   

技術的な観点からは、パブリック エンドポイントを使用すると Azure ファイル共有をかなりマウントしやすくなりますが、ほとんどのお客様は、ExpressRoute または VPN 接続経由での Azure ファイル共有のマウントを選択することが予想されます。 これを行うには、ご利用の環境に合わせて次のように構成する必要があります。  

- **ExpressRoute、サイト間、またはポイント対サイト VPN を使用したネットワーク トンネリング**:仮想ネットワークへのトンネリングでは、ポート 445 がブロックされている場合でも、オンプレミスから Azure ファイル共有にアクセスできます。
- **プライベート エンドポイント**:プライベート エンドポイントでは、仮想ネットワークのアドレス空間内から、ストレージ アカウントに専用 IP アドレスが提供されます。 これにより、Azure Storage クラスターによって所有されているすべての IP アドレス範囲に対して、オンプレミスのネットワークを開くことなくネットワーク トンネリングを行うことができます。 
- **DNS 転送**:オンプレミスの DNS を構成して、ストレージ アカウントの名前 (つまり、パブリック クラウド リージョンの `storageaccount.file.core.windows.net`) を解決し、プライベート エンドポイントの IP アドレスに解決するようにします。

Azure ファイル共有のデプロイに関連するネットワークを計画する場合は、「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」を参照してください。

## <a name="encryption"></a>暗号化
Azure Files では、2 種類の暗号化がサポートされています。転送中の暗号化は、Azure ファイル共有のマウントとアクセス時に使用される暗号化に関連しており、保存時の暗号化は、データがディスクに格納されるときの暗号化の方法に関連します。 

### <a name="encryption-in-transit"></a>転送中の暗号化
既定では、すべての Azure ストレージ アカウントで転送中の暗号化が有効になっています。 つまり、SMB 経由でファイル共有をマウントするか、または FileREST プロトコル (Azure portal、PowerShell/CLI、Azure SDK など) 経由でファイル共有にアクセスすると、Azure Files では、暗号化または HTTPS が設定されている SMB 3.0 以上で作成された接続のみが許可されます。 SMB 3.0 をサポートしていないクライアント、または SMB 3.0 をサポートしているが、SMB 暗号化をサポートしていないクライアントは、転送中の暗号化が有効になっている場合は Azure ファイル共有をマウントできません。 どのオペレーティング システムが暗号化付き SMB 3.0 をサポートしているかの詳細については、[Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md)、および [Linux](storage-how-to-use-files-linux.md) に関する当社の詳細なドキュメントを参照してください。 PowerShell、CLI、および SDK の現在のバージョンはすべて HTTPS をサポートしています。  

Azure ストレージ アカウントでの転送中の暗号化を無効にすることができます。 暗号化が無効になっている場合、Azure Files では、SMB 2.1、暗号化なしの SMB 3.0、および HTTP 経由の暗号化されていない FileREST API 呼び出しも許可されます。 転送中の暗号化を無効にする主な理由は、古いオペレーティング システム (Windows Server 2008 R2 や古い Linux ディストリビューションなど) 上で実行する必要のあるレガシ アプリケーションをサポートするためです。 Azure Files では、Azure ファイル共有と同じ Azure リージョン内の SMB 2.1 接続のみが許可されます。Azure ファイル共有の Azure リージョンの外部 (オンプレミスまたは異なる Azure リージョン内など) の SMB 2.1 クライアントは、ファイル共有にアクセスできません。

転送中のデータの暗号化が有効になっていることを確認することを強くお勧めします。

転送中の暗号化の詳細については、「[Azure Storage で安全な転送が必要](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

### <a name="encryption-at-rest"></a>保存時の暗号化
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>データ保護
Azure Files には、データがバックアップされて回復可能であり、セキュリティの脅威から保護されることを保証するための、多層化された方法が用意されています。

### <a name="soft-delete"></a>論理的な削除
ファイル共有の論理的な削除 (プレビュー) は、ファイル共有が誤って削除された場合に回復できるようにするストレージ アカウント レベルの設定です。 ファイル共有が削除された場合、完全に消去されるのではなく、論理的に削除された状態に移行します。 論理的に削除されたデータが完全に削除され、復旧できなくなるまでの時間を構成することができます。この保有期間中はいつでも共有の削除を取り消すことができます。 

ほとんどのファイル共有に対しては、論理的な削除を有効にすることをお勧めします。 共有の削除が一般的かつ望まれているワークフローでは、保持期間を非常に短く設定するか、または論理的な削除をまったく有効にしないことを決定することができます。

論理的な削除の詳細については、[データの誤削除の防止](https://docs.microsoft.com/azure/storage/files/storage-files-prevent-file-share-deletion)に関する記事を参照してください。

### <a name="backup"></a>バックアップ
[共有スナップショット](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files)を利用して、Azure ファイル共有をバックアップすることができます。これは、特定の時点の共有の読み取り専用のコピーです。 スナップショットは増分であり、以前のスナップショット以降に変更されたデータだけが含まれます。 ファイル共有ごとに最大 200 のスナップショットを保持し、最大 10 年間保存できます。 これらのスナップショットを取得するには、Azure portal、PowerShell、またはコマンドライン インターフェイス (CLI) を使用して手動で行うか、または [Azure Backup](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json) を使用することができます。 スナップショットはファイル共有内に格納されます。つまり、ファイル共有を削除すると、スナップショットも削除されます。 スナップショット バックアップを誤削除から保護するために、共有に対して論理的な削除が有効になっていることを確認します。

[Azure ファイル共有の Azure Backup ](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json)によって、スナップショットのスケジュール設定と保持期間が処理されます。 三世代 (GFS: grandfather-father-son) 機能は、日次、週次、月次、年次のスナップショットを取得できることを意味し、それぞれに個別の保持期間が設けられています。 また、Azure Backup によって論理的な削除の有効化が調整され、その中のいずれかのファイル共有がバックアップに構成されると、すぐにストレージ アカウント上で削除のロックが行われます。 最後に、顧客が自身のバックアップ資産の統合されたビューを利用できるように、Azure Backup には、特定の主な監視機能およびアラート機能が用意されています。

Azure portal 上で、Azure Backup を使用して、項目レベルおよび共有レベルの両方の復元を実行できます。 必要な作業は、復元ポイント (特定のスナップショット)、特定のファイルまたはディレクトリ (該当する場合)、復元先の場所 (元の場所または別の場所) を選択することだけです。 バックアップ サービスによって、スナップショット データのコピーが処理され、ポータル上に復元の進行状況が表示されます。

バックアップの詳細については、「[Azure ファイル共有のバックアップについて](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json)」を参照してください。

### <a name="advanced-threat-protection-for-azure-files-preview"></a>Advanced Threat Protection for Azure Files (プレビュー)
Advanced Threat Protection (ATP) for Azure Storage には、ストレージ アカウントへの通常とは異なるアクセス試行など、ストレージ アカウント上で異常なアクティビティが検出されたときにアラートを提示するセキュリティ インテリジェンスの追加レイヤーが用意されています。 また、ATP によってマルウェアのハッシュ評価分析も実行され、既知のマルウェアに関するアラート通知が行われます。 Azure Security Center を使用して、サブスクリプションまたはストレージ アカウント レベルで ATP を構成できます。 

詳細については、[Advanced Threat Protection for Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) に関するページを参照してください。

## <a name="storage-tiers"></a>ストレージ層
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

一般に、Azure Files の機能とその他のサービスとの相互運用性は Premium ファイル共有と Standard ファイル共有 (トランザクションの最適化、ホットとクールのファイル共有を含む) で同じですが、重要な違いがいくつかあります。
- **課金モデル**
    - Premium ファイル共有は、プロビジョニングされた課金モデルを使用して課金されます。これは、実際に要求するストレージの量ではなく、プロビジョニングするストレージの量に対して料金を支払うことを意味します。 
    - Standard ファイル共有は従量課金制モデルを使用して課金されます。これには、実際に消費しているストレージの量に対するストレージの基本コストと、共有の使用方法に基づく追加のトランザクション コストが含まれます。 Standard ファイル共有では、Azure ファイル共有の使用 (読み取り、書き込み、マウント) 量が増えると、課金が増加します。
- **冗長オプション**
    - Premium ファイル共有は、ローカル冗長 (LRS) およびゾーン冗長 (ZRS) ストレージでのみ使用できます。
    - Standard ファイル共有は、ローカル冗長、ゾーン冗長、geo 冗長 (GRS)、および geo ゾーン冗長 (GZRS) ストレージで使用できます。
- **ファイル共有の最大サイズ**
    - Premium ファイル共有の場合、追加作業なしで最大 100 TiB のプロビジョニングが可能です。
    - 既定では、Standard ファイル共有は最大 5 TiB にのみまたがることができますが、"*大きいファイルの共有*" ストレージ アカウント機能フラグを選択することで、共有の制限を 100 TiB に増やすことができます。 ローカル冗長ストレージ アカウントまたはゾーン冗長ストレージ アカウントの場合、Standard ファイル共有は最大 100 TiB にのみまたがることができます。 ファイル共有サイズの増加の詳細については、「[大きなファイル共有の有効化と作成](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share)」を参照してください。
- **リージョン別の提供状況**
    - Premium ファイル共有はすべてのリージョンで使用できるわけではありません。ゾーン冗長サポートは、リージョンの小さなサブセットで利用できます。 ご自分のリージョンで現在 Premium ファイル共有を使用できるかどうかを見つけるには、Azure の [[リージョン別の利用可能な製品]](https://azure.microsoft.com/global-infrastructure/services/?products=storage) ページを参照してください。 ZRS がサポートされるリージョンを確認する場合は、[リージョン別の Azure 可用性ゾーンのサポート](../../availability-zones/az-region.md)に関する記事を参照してください。 この[アンケート](https://aka.ms/pfsfeedback)にご記入ください。新しいリージョンと機能に優先順位を付けるために役立ちます。
    - Standard ファイル共有は、すべての Azure リージョンで使用できます。
- Azure Kubernetes Service (AKS) では、バージョン 1.13 以降での Premium ファイル共有がサポートされています。

ファイル共有が Premium または Standard ファイル共有として作成されると、他のレベルに自動的に変換することはできません。 他のレベルに切り替える場合は、そのレベルで新しいファイル共有を作成し、作成した新しい共有に元の共有から手動でデータをコピーする必要があります。 Windows の場合は `robocopy` を、macOS と Linux の場合は `rsync` を使用して、そのコピーを実行することをお勧めします。

### <a name="understanding-provisioning-for-premium-file-shares"></a>Premium ファイル共有のプロビジョニングについて
Premium ファイル共有は、固定 GiB/IOPS/スループット比に基づいてプロビジョニングされます。 プロビジョニングされた GiB ごとに、共有は、1 IOPS と 0.1 MiB/秒のスループットから、共有ごとの最大限度まで発行されます。 最小許容プロビジョニングは 100 GiB で、最小の IOPS/スループットになります。

ベスト エフォート方式では、すべての共有は、プロビジョニングされたストレージの GiB ごとに 3 IOPS まで、共有のサイズに応じて 60 分またはそれ以上バーストできます。 新しい共有は、プロビジョニングされた容量に基づく完全なバースト クレジットで開始されます。

共有は 1 GiB 単位でプロビジョニングする必要があります。 最小サイズは 100 GiB、次のサイズは 101 GiB、以下同様です。

> [!TIP]
> ベースライン IOPS = 1 * プロビジョニング済み GiB。 (最大 100,000 IOPS まで)。
>
> バースト限度 = 3 * ベースライン IOPS。 (最大 100,000 IOPS まで)。
>
> エグレス レート = 60 MiB/秒 + 0.06 * のプロビジョニング済み GiB
>
> イングレス レート = 40 MiB/秒 + 0.04 * のプロビジョニング済み GiB

プロビジョニングされた共有サイズは、共有クォータによって指定されます。 共有クォータの拡大はいつでも実行できますが、縮小は前回の拡大から 24 時間経過した後にのみ実行できます。 クォータの拡大なしで 24 時間経過した後は、再び拡大するまで、共有クォータを何回でも縮小できます。 IOPS/スループットのスケールの変更は、サイズ変更後、数分以内に有効になります。

プロビジョニングされた共有のサイズを、使用されている GiB 未満に縮小できます。 これを行った場合、データは失われませんが、使用されているサイズに対して引き続き課金され、使用されているサイズではなく、プロビジョニングされた共有のパフォーマンス (ベースライン IOPS、スループット、およびバースト IOPS) を受け取ります。

次の表は、プロビジョニングされた共有サイズについてのこれらの式の例をいくつか示しています。

|容量 (GiB) | ベースライン IOPS | バースト IOPS | エグレス (MiB/秒) | イングレス (MiB/秒) |
|---------|---------|---------|---------|---------|
|100         | 100     | 最大 300     | 66   | 44   |
|500         | 500     | 最大 1,500   | 90   | 60   |
|1,024       | 1,024   | 最大 3,072   | 122   | 81   |
|5,120       | 5,120   | 最大 15,360  | 368   | 245   |
|10,240      | 10,240  | 最大 30,720  | 675 | 450   |
|33,792      | 33,792  | 最大 100,000 | 2,088 | 1,392   |
|51,200      | 51,200  | 最大 100,000 | 3,132 | 2,088   |
|102,400     | 100,000 | 最大 100,000 | 6,204 | 4,136   |

> [!NOTE]
> ファイル共有のパフォーマンスは、他の多くの要因の中でも特にマシン ネットワークの制限、使用可能なネットワーク帯域幅、IO サイズ、並列処理の影響を受けます。 たとえば、8 KiB の読み取り/書き込み IO サイズでの内部テストに基づいて、SMB 経由で Premium ファイル共有に接続された単一の Windows 仮想マシン (*Standard F16s_v2*) は 20K の読み取り IOPS と 15K の書き込み IOPS を実現できます。 512 MiB の読み取り/書き込み IO サイズでは、同じ VM は 1.1 GiB/秒の送信スループットと 370 MiB/秒の受信スループットを実現できます。 最大のパフォーマンス スケールを達成するには、負荷を複数の VM に分散します。 一般的なパフォーマンスの問題と回避策については、[トラブルシューティング ガイド](storage-troubleshooting-files-performance.md)に関するページを参照してください。

#### <a name="bursting"></a>バースト
Premium ファイル共有は、最大 3 倍の IOPS をバーストできます。 バーストは自動化され、クレジット システムに基づいて動作します。 バーストはベスト エフォートで動作し、バースト限度は保証されるものではなく、ファイル共有は限度*まで*バーストすることができます。

クレジットは、ファイル共有のトラフィックがベースライン IOPS を下回るたびに、バースト バケットに蓄積されます。 たとえば、100 GiB 共有には 100 ベースライン IOPS が含まれます。 共有の実際のトラフィックが特定の 1 秒間で 40 IOPS だった場合は、未使用の 60 IOPS がバースト バケットに補充されます。 これらのクレジットは、後で操作がベースライン IOP を超えたときに使用されます。

> [!TIP]
> バースト バケットのサイズ = ベースライン IOPS * 2 * 3600。

共有は、ベースライン IOPS を超え、バースト バケット内にクレジットがあるときは常にバーストします。 共有はクレジットが残っている限りはバーストを続行できるため、50 TiB より小さい共有のみが最大 1 時間の間、バースト限度で維持されます。 50 TiB より大きい共有は、技術的にはこの 1 時間の限度を超えて最大 2 時間まで可能ですが、これは発生したバースト クレジットの数に基づきます。 ベースライン IOPS を超えた IO のそれぞれが 1 つのクレジットを消費し、すべてのクレジットが消費されると、共有はベースライン IOPS に戻ります。

共有のクレジットには次の 3 つの状態があります。

- 蓄積中 (ファイル共有が使用している量がベースライン IOPS より少ない場合)。
- 減少中 (ファイル共有がバースト中の場合)。
- 定数を維持 (クレジットがないか、ベースライン IOPS が使用中の場合)。

新しいファイル共有は、そのバースト バケットに全数のクレジットが含まれると開始されます。 サーバーによる調整のために共有 IOPS がベースライン IOPS を下回った場合、バースト クレジットは発生しません。

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Standard ファイル共有を最大 100 TiB にまたがることができるようにする
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>制限事項
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>冗長性
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>移行
多くの場合、組織に対して新しいファイル共有を確立するのではなく、既存のファイル共有をオンプレミスのファイル サーバーまたは NAS デバイスから Azure Files に移行します。 移行を成功させるには、シナリオに適した移行戦略とツールを選択することが重要です。 

[移行の概要に関する記事](storage-files-migration-overview.md)に、基本についての説明と、シナリオに適した移行ガイドを紹介する表が含まれています。

## <a name="next-steps"></a>次のステップ
* [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
* [Azure Files のデプロイ方法](storage-files-deployment-guide.md)
* [Azure ファイル同期のデプロイ方法](storage-sync-files-deployment-guide.md)
* [シナリオに適した移行ガイドを見つけるには、移行の概要に関する記事をご覧ください。](storage-files-migration-overview.md)
