---
title: StorSimple 5000-7000 シリーズデバイスからのデータ移行オプション
description: StorSimple 5000-7000 シリーズのデータを移行するためのオプションの概要を示します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 316b513f761aa422f87c83658e6c50efec25efb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94961143"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>StorSimple 5000-7000 シリーズのデータを移行するためのオプション 

> [!IMPORTANT]
> 2019 年 7 月 9 日に、StorSimple 5000/7000 シリーズはサポート終了 (EOS) になります。 StorSimple 5000/7000 シリーズをご利用のお客様には、このドキュメントで説明する他のいずれかのシリーズへの移行をお勧めします。

2019年 7 月に StorSimple 5000-7000 シリーズの[サポートは終了](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series)します。 StorSimple 5000-7000 シリーズを実行しているお客様は、他の Azure ファースト パーティのハイブリッド サービスにアップグレードできます。 この記事では、データの移行に使用できる Azure ハイブリッド オプションについて説明します。 

## <a name="migration-options"></a>移行オプション

StorSimple 5000-7000 シリーズをご使用のお客様には、Azure またはサード パーティのオプションがあります。

### <a name="azure-options"></a>Azure のオプション

#### <a name="upgrade-to-storsimple-8000-series"></a>StorSimple 8000 シリーズにアップロードする

StorSimple 8000 シリーズにアップグレードして、StorSimple プラットフォームを引き続き使用します。  このアップグレード パスでは、お客様は 5000-7000 シリーズ デバイスを 8000 シリーズで置き換える必要があります。 データは、移行ツールを使用して、5000-7000 シリーズ デバイスから移行されます。 移行が正常に完了すると、StorSimple 8000 シリーズ デバイスにより、Azure Blob Storage にデータが引き続き格納されます。 

StorSimple 8000 シリーズを使用してデータを移行する方法の詳細については、「[StorSimple 5000-7000 シリーズ デバイスのデータを 8000 シリーズ デバイスに移行する](storsimple-8000-migrate-from-5000-7000.md)」を参照してください。

#### <a name="migrate-to-azure-file-sync"></a>Azure File Sync への移行

この新しい移行オプションを使用すると、お客様は、所属する組織のファイル共有を Azure Files に格納することができます。 その後、これらのファイル共有は、オンプレミス アクセスのために Azure File Sync (AFS) を使用して一元化されます。 AFS は Windows Server ホストにデプロイできます。 以降、実際のデータ移行は、ホスト コピーとして、または移行ツールを使用して実行されます。

Azure File Sync にデータを移行する方法の詳細については、[StorSimple 5000-7000 シリーズから Azure File Sync へのデータの移行](../storage/files/storage-files-migration-storsimple-8000.md)に関するページをご覧ください。

### <a name="third-party-options"></a>サード パーティのオプション

#### <a name="migrate-to-panzura-freedom-nas"></a>Panzura Freedom NAS に移行する

StorSimple 5000-7000 をご使用のお客様は、Azure でのデータを保持するために、Panzura Freedom NAS への移行を選択できます。 Panzura Freedom ソリューションには、データセンター、オフィス、パブリック クラウドおよびプライベート クラウドにまたがる NAS ソリューションが用意されています。 このソリューションにより、NFS、SMB、およびモバイル クライアントのローカル、ハイブリッド、およびクラウド内のデータ ワークフローが有効になります。 

この移行は Panzura によってサポートされており、お客様は [Panzura Web サイト](https://panzura.com/storsimple-migration/)から移行サポートを要求することで開始することができます。

#### <a name="migrate-to-cohesity"></a>Cohesity に移行する

Cohesity を使用すると、現在の StorSimple 5000–7000 から Azure の Cohesity Data Platform にデータを移行できます。 Cohesity Data Platform はソフトウェアによる Web スケール ソリューションであり、ファイル、バックアップ、オブジェクト、VM を 1 つのクラウドネイティブ ソリューションに統合します。 このデータ プラットフォームに移行後、1 か所からデータやアプリを管理し、保護し、クラウドからコアにプロビジョニングできます。 Cohesity は、3 つという少ない数のノードから始められます。 

詳細については、[Cohesity DataPlatform への移行](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html)に関するページを参照してください。

#### <a name="migrate-to-nasuni"></a>Nasuni への移行

Nasuni によって StorSimple 5000-7000 をご使用のお客様は、そのデータを Azure に簡単に移行して保持することができます。  Nasuni は最新の Azure ベースの NAS ストレージ ソリューションであり、クラウドの経済性とスケールにより、お客様にオンプレミス ソリューションに想定されるパフォーマンスとセキュリティを提供します。  ハイ パフォーマンスのファイル ストレージに加えて、Nasuni と Azure はバックアップと DR を処理する一方で、一元的なファイル ストレージ管理によって世界中でのデータの共有とコラボレーションを可能にします。 

Nasuni では簡単に移行できます ‐ 今すぐ開始しましょうhttps://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Talon FAST に移行する

StorSimple 5000-7000 をお使いのお客様は、Talon にすることで、StorSimple プラットフォームで得られていたメリット (無制限のクラウド リソースでサポートされた小さいオンサイト フットプリント) を、いっそう優れた機能で引き続き利用できます。  Talon FAST ソリューションでは、お客様はデータを移行して Azure に保持できますが、さらに小さいソフトウェア専用のオンサイト フットプリントが用意され、グローバル ファイル ロック、グローバル名前空間、マルチサイト コラボレーションなどのベネフィットが加わっています。  Talon は、世界中のお客様と協力して、そのオンプレミスのファイル サーバーのワークロードを、ユーザー ワークフローまたはエクスペリエンスを損なうことなく、統合された Azure ベースのフットプリントに移行するための有数の Azure エコシステム ソリューションです。  

クラウド統合エンタープライズに発展する方法の詳細については、https://www.talonstorage.com/alliances/microsoft-storsimple を参照してください。


## <a name="migration---frequently-asked-questions"></a>移行 - よく寄せられる質問

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>Q. StorSimple 5000 および 7000 シリーズのデバイスのサービスはいつ終了しますか。 

A. StorSimple 5000-7000 シリーズは 2019 年 7 月に[サービスが終了](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series)になります。 2019 年 7 月のサービス終了後、これらのデバイスのソフトウェアとハードウェア両方について、Microsoft ではサポートを提供できなくなります。 お使いのデバイスのデータを移行する計画を今すぐ立て始めることを強くお勧めします。

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. Azure に格納したデータはどうなりますか。  

A. 新しいサービスにデータを移行すれば、そのデータは Azure で引き続き使用できます。 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. StorSimple デバイスにローカルに格納したデータはどうなりますか。 

A. ローカル デバイスのデータを新しいサービスにコピーするには、移行ドキュメントの説明に従ってください。

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>Q. StorSimple 5000/7000 シリーズ アプライアンスをそのまま使い続けたい場合は、どうなりますか。 

A. サービスは動作し続ける可能性がありますが、Microsoft では、ハードウェアとソフトウェアのサポートを提供できなくなります。 ビジネス継続性を実現するめに移行することを強くお勧めします。

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>Q. StorSimple 5000-7000 シリーズのデバイスからのデータ移行オプションには、どのようなものがありますか。 

A. StorSimple 5000-7000 シリーズのユーザーは、それぞれのシナリオに応じて、次の移行オプションを使用できます。 

 - **8000 シリーズへのアップグレード**: このオプションは、StorSimple プラットフォームを使用し続ける場合に使用します。 
 - **Azure File Sync に移行**:このオプションは、Azure ネイティブ形式に切り替える場合に使用します。 Azure File Sync を使用すると、ファイル共有を一元管理できます。 

ここで紹介していない移行オプションについては、Microsoft サポートにお問い合わせください。

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. 他のストレージ ソリューションへの移行はサポートされていますか。

A. はい。 他のストレージ ソリューションへの移行には、データのホスト コピーを使用できます。

### <a name="q-is-migration-supported-by-microsoft"></a>Q. 移行は Microsoft によってサポートされていますか。 

A. 5000 または 7000 シリーズからの移行は完全にサポートされています。 実際、Microsoft では、移行を開始する前に、サポートに問い合わせることをお勧めしています。 現在、移行操作には Microsoft サポートの支援が必要です。 StorSimple 5000-7000 シリーズのデバイスからデータを移行する場合は、[サポート チケットを開いてください](storsimple-8000-contact-microsoft-support.md)。

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>Q. 2 つの移行オプションの価格モデルはどのようなものですか。

A. 移行のコストは、選択するオプションによって異なります。 移行自体は無料ですが、StorSimple 8000 シリーズにアップグレードする場合は、ハードウェア デバイスのコストがかかります。 

同様に、Azure File Sync を使用する場合は、サービスに対するサブスクリプション料金が適用されます。 いずれの場合も、お客様はストレージ コストも継続的に支払う必要があります。 見積もりについては、以下を参照してください。 
- [StorSimple の価格](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS の価格]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>Q.  移行が完了するまでの時間はどのくらいですか。

A. データの移行にかかる時間は、データ量と選択したアップグレード オプションによって異なります。 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>Q. StorSimple 8000 シリーズのサポート終了日はいつですか。

A. StorSimple 8000 シリーズのサポート終了日については、[こちら](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)で公開されています。


## <a name="next-steps"></a>次のステップ
 - [StorSimple 5000-7000 シリーズ デバイスのデータを 8000 シリーズ デバイスに移行する](storsimple-8000-migrate-from-5000-7000.md)。
 - [StorSimple 5000-7000 シリーズ デバイスのデータを Azure File Sync に移行する](../storage/files/storage-files-migration-storsimple-8000.md)