---
title: Azure NetApp Files の新機能 | Microsoft Docs
description: Azure NetApp Files の最新の新機能と機能強化の概要について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: f2167159b03cd0387acfccf4bbd0a2e840f739df
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955234"
---
# <a name="whats-new-in-azure-netapp-files"></a>Azure NetApp Files の新機能

Azure NetApp Files は定期的に更新されています。 この記事では、最新の新機能と機能強化の概要について説明します。 

## <a name="march-2021"></a>2021 年 3 月

* [SMB 継続的可用性 (CA) 共有](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (プレビュー)  

    SMB 透過フェールオーバーを使用すると、SMB ボリュームにデータを格納してアクセスするサーバー アプリケーションへの接続を中断することなく、Azure NetApp Files サービスでメンテナンス操作を行うことができます。 SMB 透過フェールオーバーをサポートするために、Azure NetApp Files では、Azure VM で実行されている SMB を介して SQL Server アプリケーションで使用する SMB 継続的可用性共有オプションがサポートされるようになりました。 この機能は現在、Windows SQL Server でサポートされています。 Linux SQL Server では現在サポートされていません。 この機能を有効にすると、[単一インスタンス、Always-On フェールオーバー クラスター インスタンス、Always-On 可用性グループの展開](azure-netapp-files-solution-architectures.md#sql-server)において、SQL Server のパフォーマンスが大幅に向上し、スケーリングとコスト面の利点が得られます。 「[SQL Server の展開に Azure NetApp Files を使用する利点](solutions-benefits-azure-netapp-files-sql-server.md)」を参照してください。

* [リージョン間レプリケーションの宛先ボリュームの自動サイズ変更](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    リージョン間のレプリケーション関係では、宛先ボリュームは、ソース ボリュームのサイズに基づいて、自動的にサイズ変更されます。 そのため、宛先ボリュームを個別にサイズ変更する必要はありません。 この自動サイズ変更の動作は、ボリュームがアクティブなレプリケーション関係にある場合、または再同期操作でレプリケーション ピアリングが切断された場合に適用されます。 この機能を使用するには、ソースと宛先の両方のボリュームの容量プールに十分なヘッドルームを確保する必要があります。

## <a name="december-2020"></a>2020 年 12 月

* [Azure アプリケーション整合性スナップショット ツール](azacsnap-introduction.md) (プレビュー)    

    Azure アプリケーション整合性スナップショット ツール (AzAcSnap) は、Linux 環境 (SUSE、RHEL など) におけるサードパーティ データベース (SAP HANA) のデータ保護を省力化するコマンドライン ツールです。   

    AzAcSnap では、Azure NetApp Files と Azure Large Instance のボリューム スナップショット機能およびレプリケーション機能が活用されます。 これには、次のようなメリットがあります。

    * アプリケーション整合性のデータ保護 
    * データベース カタログ管理 
    * "*アドホック*" ボリューム保護 
    * 記憶域ボリュームの複製 
    * ディザスター リカバリーのサポート 

## <a name="november-2020"></a>2020 年 11 月

* [スナップショットの復元](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    スナップショットを元に戻す機能を使用すると、特定のスナップショットが作成されたときの状態にボリュームをすばやく戻すことができます。 ほとんどの場合、ボリュームを元に戻す方が、個々のファイルをスナップショットからアクティブなファイル システムに復元するよりもはるかに高速です。 また、スナップショットを新しいボリュームに復元する場合と比べて、スペース効率が高くなります。

## <a name="september-2020"></a>2020 年 9 月

* [Azure NetApp Files のリージョン間レプリケーション](cross-region-replication-introduction.md) (プレビュー)

  Azure NetApp Files で、リージョン間レプリケーションがサポートされるようになりました。 この新しいディザスター リカバリー機能を使用することで、Azure NetApp Files のボリュームをある Azure リージョンから別のリージョンに、素早くかつコスト効率の良い方法でレプリケートすることができます。これにより、予測不可能なリージョンの障害からデータを保護することができます。 Azure NetApp Files のリージョン間レプリケーションでは、NetApp の SnapMirror® 技術を活用しています。これにより、変更されたブロックのみが、圧縮された効率的な形式でネットワーク経由で送信されます。 この独自のテクノロジにより、リージョン間でのレプリケーションに必要なデータ量が最小化されるため、データ転送コストが削減されます。 また、レプリケーションにかかる時間が短縮されるため、より小さい回復ポイントの目標 (RPO) を実現できます。

* [手動 QoS 容量プール](manual-qos-capacity-pool-introduction.md) (プレビュー)  

    手動 QoS 容量プールでは、ボリュームの容量とスループットを個別に割り当てることができます。 手動 QoS 容量プールで作成されたすべてのボリュームの合計スループットは、プールの合計スループットによって制限されます。 これは、プール サイズとサービスレベルのスループットの組み合わせによって決まります。 または、容量プールの [[QoS type]\(QoS の種類\)](azure-netapp-files-understand-storage-hierarchy.md#qos_types) を既定の設定である [auto]\(自動\) にすることもできます。 自動 QoS 容量プールでは、ボリュームに割り当てられたサイズ クォータに比例して、スループットがプール内のボリュームに自動的に割り当てられます。

* [LDAP 署名](azure-netapp-files-create-volumes-smb.md) (プレビュー)   

    Azure NetApp Files で、Azure NetApp Files サービスとユーザー指定の Active Directory Domain Services ドメイン コントローラーとの間の LDAP 参照をセキュリティで保護する LDAP 署名がサポートされるようになりました。 現在、この機能はプレビュー段階にあります。

* [AD 認証の AES 暗号化](azure-netapp-files-create-volumes-smb.md) (プレビュー)

    Azure NetApp Files で、SMB ボリュームの AES 暗号化を有効にする、DC への LDAP 接続で AES 暗号化がサポートされるようになりました。 現在、この機能はプレビュー段階にあります。 

* 新しい[メトリック](azure-netapp-files-metrics.md):   

    * 新しいボリューム メトリック: 
        * *ボリュームの割り当てサイズ*:ボリュームのプロビジョニングされたサイズ
    * 新しいプールのメトリック: 
        * *プールの割り当てサイズ*:プールのプロビジョニングされたサイズ 
        * *プールのスナップショット サイズの合計*:プール内のすべてのボリュームからのスナップショット サイズの合計

## <a name="july-2020"></a>2020 年 7 月

* [デュアルプロトコル (NFSv3 と SMB) ボリューム](create-volumes-dual-protocol.md)

    デュアルプロトコル (NFS v3 と SMB) での同時アクセスを可能にし、LDAP ユーザー マッピングをサポートする、Azure NetApp Files ボリュームを作成できるようになりました。 この機能により、生成したデータを Azure NetApp Files ボリュームに格納する Linux ベースのワークロードが存在する場合があるユース ケースに対応できます。 それと同時に、スタッフは Windows ベースのクライアントとソフトウェアを使用して、同じ Azure NetApp Files ボリュームの新しく生成されたデータを分析する必要があります。 デュアルプロトコルでの同時アクセス機能を利用すると、ワークロードによって生成されたデータを事後分析のために別のプロトコルを使用して別のボリュームにコピーする必要がなくなるため、ストレージのコストと運用時間を節約できます。 この機能は無料で (通常の [Azure NetApp Files のストレージ コスト](https://azure.microsoft.com/pricing/details/netapp/)は適用されます)、一般提供になっています。 詳細については、[デュアルプロトコルでの同時アクセスに関するドキュメント](create-volumes-dual-protocol.MD)を参照してください。

* [転送中の NFS v4.1 Kerberos 暗号化](configure-kerberos-encryption.MD)

    Azure NetApp Files で、AES-256 暗号化を使用した Kerberos モード (krb5、krb5i、krb5p) での NFS クライアント暗号化がサポートされるようになったため、データ セキュリティが強化されます。 この機能は無料で (通常の [Azure NetApp Files のストレージ コスト](https://azure.microsoft.com/pricing/details/netapp/)は適用されます)、一般提供になっています。 詳細については、[NFS v4.1 Kerberos 暗号化に関するドキュメント](configure-kerberos-encryption.MD)を参照してください。

* [ボリュームのサービス レベルの動的な変更](dynamic-change-volume-service-level.MD)

    クラウドでは IT の支出における柔軟性が期待されています。 ボリュームに必要なサービス レベルを使用している別の容量プールにボリュームを移動することで、既存の Azure NetApp Files ボリュームのサービス レベルを変更できるようになりました。 ボリュームのサービス レベルのインプレース変更では、データを移行する必要はありません。 ボリュームに対するデータ プレーン アクセスにも影響はありません。 既存のボリュームを変更し、パフォーマンス向上のために上位のサービス レベルを使用することも、コスト最適化のために下位のサービス レベルを使用することもできます。 この機能は無料で (通常の [Azure NetApp Files のストレージ コスト](https://azure.microsoft.com/pricing/details/netapp/)は適用されます)、パブリック プレビューになっています。 機能のプレビューに登録するには、[ボリュームのサービス レベルの動的な変更に関するドキュメント](dynamic-change-volume-service-level.md)を参照してください。

* [ボリュームのスナップショット ポリシー](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (プレビュー) 

    Azure NetApp Files を利用すると、ボリュームの特定の時点のスナップショットを作成できます。 今後は、選択した頻度で Azure NetApp Files によってボリュームのスナップショットが自動的に作成されるよう指定するスナップショット ポリシーを作成できます。 スナップショットが毎時、毎日、毎週、または毎月のサイクルで作成されるようにスケジュールを設定できます。 スナップショット ポリシーの一環として、保持するスナップショットの最大数を指定することもできます。 この機能は無料で (通常の [Azure NetApp Files のストレージ コスト](https://azure.microsoft.com/pricing/details/netapp/)は適用されます)、プレビューになっています。 [ボリュームのスナップショット ポリシーのドキュメント](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)に従って、機能のプレビューに登録できます。

* [NFS ルート アクセスのエクスポート ポリシー](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files で、ルート アカウントからボリュームにアクセスできるかどうかを指定できるようになりました。 

* [スナップショット パスの非表示](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    マウントされたボリューム上の `.snapshot` ディレクトリ (NFS クライアント) または `~snapshot` フォルダー (SMB クライアント) をユーザーが参照してアクセスできるかどうかを Azure NetApp Files で指定できるようになりました。

## <a name="may-2020"></a>2020 年 5 月

* [バックアップ ポリシー ユーザー](create-active-directory-connections.md) (プレビュー)

    Azure NetApp Files では、Azure NetApp Files で使用するために作成されたコンピューター アカウントに対して昇格された特権を必要とする追加のアカウントを含めることができます。 指定したアカウントは、ファイルまたはフォルダー レベルで NTFS アクセス許可を変更できます。 たとえば、Azure NetApp Files の SMB ファイル共有にデータを移行するために使用される非特権サービス アカウントを指定できます。 バックアップ ポリシー ユーザー機能は、現在プレビューの段階です。

## <a name="next-steps"></a>次の手順
* [Azure NetApp Files とは](azure-netapp-files-introduction.md)
* [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md) 
