---
title: Azure Migrate の Collector アプライアンスについて | Microsoft Docs
description: Azure Migrate の Collector アプライアンスに関する情報を提供します。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 224511b9748c540f2cd48a3d8393a9c74f76ce32
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498419"
---
# <a name="about-the-collector-appliance"></a>Collector アプライアンスについて

 この記事では、Azure Migrate Collector に関する情報を提供します。

Azure Migrate Collector は、Azure に移行する前の [Azure Migrate](migrate-overview.md) サービスによる評価を目的として、オンプレミス vCenter 環境を検出するために使用される軽量アプライアンスです。  

## <a name="discovery-method"></a>検出方法

以前、Collector アプライアンスには、1 回限りの検出と、継続的な検出という 2 つの選択肢がありましたが、 1 回限りの検出が廃止になりました。この検出はパフォーマンス データの収集を vCenter Server の統計設定に依存しており (統計設定をレベル 3 に設定する必要あり)、また、ピークではなく、平均数を収集し、結果的に規模が小さくなっていたためです。 継続的な検出モデルではデータが詳細に収集され、ピーク数を収集するため、規模が正確です。 しくみは次のとおりです。

Collector アプライアンスは Azure Migrate プロジェクトに常に接続され、VM のパフォーマンス データを継続的に収集します。

- Collector は、20 秒ごとにリアルタイムの使用状況データを収集するために、オンプレミス環境をプロファイルします。
- アプライアンスは、20 秒のサンプルをロールアップし、15 分ごとに 1 つのデータ ポイントを作成します。
- データ ポイントを作成するために、アプライアンスは 20 秒のサンプルからピーク値を選択し、それを Azure に送信します。
- このモデルは、パフォーマンス データを収集するために vCenter Server の統計設定に依存していません。
- 継続的なプロファイルは、Collector からいつでも停止できます。

**迅速な評価:** 連続検出アプライアンスでは、検出が完了すると (VM の数によっては数時間かかります)、すぐに評価を作成できます。 検出を開始するとパフォーマンス データ コレクションが開始されるため、迅速な評価を求める場合は、評価のサイズ設定基準として*オンプレミス*を選択する必要があります。 パフォーマンスベースの評価では、信頼できるサイズの推奨を得るには、検出を開始してから少なくとも 1 日待つことをお勧めします。

アプライアンスはパフォーマンス データのみを継続的に収集し、オンプレミス環境での構成の変更 (VM の追加、削除、ディスクの追加など) は検出されません。 オンプレミス環境で構成の変更がある場合は、次の操作を行って、変更をポータルに反映することができます。

- 項目 (VM、ディスク、コアなど) の追加:これらの変更を Azure portal に反映するには、アプライアンスで検出を停止してから、再開します。 これにより、Azure Migrate プロジェクトで変更が確実に更新されます。

- VM の削除:アプライアンスの設計方法のため、検出を停止して開始しても VM の削除は反映されません。 これは、後続の検出のデータが古い検出に追加され、上書きされないためです。 この場合、VM をグループから削除し、評価を再計算して、ポータルの VM は単に無視することができます。

> [!NOTE]
> 1 回限りの検出アプライアンスは推奨されなくなりました。この方法は、パフォーマンス データ ポイントの可用性と収集された平均パフォーマンス カウンターの vCenter Server の統計設定に依存しており、その結果、Azure への移行のために VM のサイズが小さくなったためです。

## <a name="deploying-the-collector"></a>Collector のデプロイ

OVF テンプレートを使用して、Collector アプライアンスをデプロイします。

- Azure portal で Azure Migrate プロジェクトから OVF テンプレートをダウンロードします。 Collector アプライアンスの仮想マシンを設定するには、ダウンロードしたファイルを vCenter Server にインポートします。
- VMware は、OVF から 8 コア、16 GB の RAM、80 GB の 1 ディスクを備えた仮想マシンを設定します。 オペレーティング システムは Windows Server 2016 (64 ビット) です。
- Collector を実行すると、Collector が Azure Migrate に接続できることを確認するために、いくつかの前提条件の確認が実行されます。

- Collector の作成について[説明します](tutorial-assessment-vmware.md#create-the-collector-vm)。


## <a name="collector-prerequisites"></a>Collector の前提条件

Collector が Azure Migrate サービスに接続し、検出されたデータをアップロードするためには、いくつかの前提条件チェックに合格する必要があります。

- **Azure クラウドの確認**: 移行先として予定している Azure クラウドを Collector に認識させる必要があります。
    - Azure Government クラウドに移行する予定であれば、Azure Government を選択します。
    - 商用の Azure クラウドに移行する予定であれば、Azure Global を選択します。
    - ここで指定されるクラウドに基づき、検出されたメタデータがアプライアンスから個々のエンド ポイントに送信されます。
- **インターネット接続の確認**: Collector がインターネットに直接、またはプロキシ経由で接続できることを確認します。
    - 前提条件の確認によって、[必須および省略可能な URL](#urls-for-connectivity) への接続が検証されます。
    - インターネットへの直接接続があれば、Collector が必須の URL にアクセスできることを確認する以外は、特別のアクションは不要です。
    - プロキシ経由で接続する場合には、以下の要件に注意してください。
- **時刻の同期の確認**: サービスに対する要求が認証されるには、Collector の時刻がインターネット時刻サーバーに同期している必要があります。
    - 時刻を検証できるよう、portal.azure.com の URL にコレクターから接続できる必要があります。
    - マシンが同期していない場合は、Collector 仮想マシンのクロックの時刻を現在の時刻と一致するよう変更する必要があります。 これを実行するには、仮想マシン上で管理プロンプトを開き、**w32tm /tz** を実行してタイム ゾーンをチェックします。 時刻を同期するには、**w32tm /resync** を実行します。
- **コレクター サービスの実行状態の確認**: Azure Migrate Collector サービスが Collector 仮想マシンで実行されている必要があります。
    - このサービスは、マシンが起動したときに自動的に開始されます。
    - サービスが実行していない場合は、コントロール パネルから開始します。
    - Collector サービスは、vCenter Server に接続し、仮想マシンのメタデータとパフォーマンス データを収集し、それを Azure Migrate サービスに送信します。
- **VMware PowerCLI 6.5 のインストール状態の確認**: vCenter Server と通信できるように、Collector 仮想マシン上に VMware PowerCLI 6.5 PowerShell モジュールをインストールする必要があります。
    - Collector がモジュールをインストールするために必要な URL にアクセスできる場合、インストールは Collector のデプロイ中に自動的に実行されます。
    - Collector でデプロイ中にモジュールがインストールできない場合は、手動でインストールする必要があります。
- **vCenter Server への接続の確認**: Collector は vCenter Server に接続し、仮想マシン、そのメタデータ、パフォーマンス カウンターのクエリを実行できなければなりません。 接続の[前提条件を確認](#connect-to-vcenter-server)します。


### <a name="connect-to-the-internet-via-a-proxy"></a>プロキシ経由でのインターネットへの接続

- プロキシ サーバーで認証が必要な場合は、Collector を設定するときにユーザー名とパスワードを指定できます。
- プロキシ サーバーの IP アドレスまたは FQDN は、*http:\//IPaddress* または *http:\//FQDN* と指定する必要があります。
- サポートされるのは HTTP プロキシのみです。 HTTPS ベースのプロキシ サーバーは、Collector ではサポートされていません。
- プロキシ サーバーがインターセプト プロキシである場合は、Collector 仮想マシンにプロキシ証明書をインポートする必要があります。
  1. Collector 仮想マシンで、**[スタート メニュー]** > **[コンピューター証明書の管理]** に移動します。
  2. 証明書ツールの **[証明書 - ローカル コンピューター]** で、**[信頼された発行元]** > **[証明書]** を探します。

      ![証明書ツール](./media/concepts-intercepting-proxy/certificates-tool.png)

  3. Collector 仮想マシンにプロキシ証明書をコピーします。 証明書はネットワーク管理者から取得することが必要になる場合があります。
  4. 証明書をダブルクリックして開き、**[証明書のインストール]** をクリックします。
  5. 証明書インポート ウィザードで、[ストアの場所] に移動して、**[ローカル マシン]** を選択します。

     ![証明書ストアの場所](./media/concepts-intercepting-proxy/certificate-store-location.png)

  6. **[証明書をすべて次のストアに配置する]** > **[参照]** > **[信頼された発行元]** を選択します。 **[完了]** をクリックして、証明書をインポートします。

     ![証明書ストア](./media/concepts-intercepting-proxy/certificate-store.png)

  7. 証明書が予期どおりインポートされていることを確認し、インターネット接続前提条件チェックが予期どおり機能していることを確認します。


### <a name="urls-for-connectivity"></a>接続の URL

接続チェックは、URL のリストに接続することによって検証されます。

**URL** | **詳細**  | **前提条件のチェック**
--- | --- | ---
*.portal.azure.com | Azure Global に適用されます。 Azure サービス、および時刻の同期との接続を確認します。 | URL へのアクセスが必要です。<br/><br/> 接続がないと、前提条件チェックは失敗します。
*.portal.azure.us | Azure Government にのみ適用されます。 Azure サービス、および時刻の同期との接続を確認します。 | URL へのアクセスが必要です。<br/><br/> 接続がないと、前提条件チェックは失敗します。
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| PowerShell vCenter PowerCLI モジュールをダウンロードするために使用されます。 | URL へのアクセスが必要です。<br/><br/> 前提条件チェックは失敗しません。<br/><br/> Collector 仮想マシンで自動のモジュールのインストールは失敗します。 インターネットに接続しているマシンに手動でモジュールをインストールしたうえで、アプライアンスにモジュールをコピーする必要があります。 [詳細については、このトラブルシューティング ガイドの手順 4 を参照してください](https://docs.microsoft.com/azure/migrate/troubleshooting-general#error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception)。


### <a name="install-vmware-powercli-module-manually"></a>VMware PowerCLI モジュールの手動でのインストール

1. このモジュールは[次の手順](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html)を使用してインストールします。 次の手順では、オンラインおよびオフラインの両方のインストールについて説明します。
2. Collector 仮想マシンがオフラインであり、インターネットにアクセスできる別のコンピューターにモジュールをインストールする場合は、そのコンピューターから Collector 仮想マシンに VMware.* ファイルをコピーする必要があります。
3. インストール後に、PowerCLI がインストールされていることを確認するために、前提条件チェックを再起動することができます。

### <a name="connect-to-vcenter-server"></a>vCenter Server への接続

Collector は、vCenter Server に接続し、仮想マシンのメタデータとパフォーマンス カウンターのクエリを実行します。 この接続で必要なものを次に示します。

- vCenter Server のバージョン 5.5、6.0、6.5、6.7 のみがサポートされています。
- 検出用に、以下に要約されているアクセス許可を持つ読み取り専用のアカウントが必要です。 検出のためにアクセスできるのは、アカウントでアクセス可能なデータ センターのみです。
- 既定では、FQDN または IP アドレスで vCenter Server に接続します。 vCenter Server が別のポートでリッスンしている場合は、フォーム *IPAddress:Port_Number* または *FQDN:Port_Number* を使用してそれに接続します。
- コレクターには、vCenter サーバーにつながるネットワーク接続が必要です。

#### <a name="account-permissions"></a>アカウントのアクセス許可

**アカウント** | **アクセス許可**
--- | ---
読み取り専用以上のユーザー アカウント | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用   


## <a name="collector-communications"></a>Collector の通信

Collector は、次の図と表にまとめたように通信します。

![コレクターの通信ダイアグラム](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Collector の通信先** | **ポート** | **詳細**
--- | --- | ---
Azure Migrate サービス | TCP 443 | Collector は、SSL 443 経由で Azure Migrate サービスと通信します。
vCenter Server | TCP 443 | Collector は、vCenter Server と通信できる必要があります。<br/><br/> 既定では、443 経由で vCenter に接続します。<br/><br/> vCenter Server が別のポートをリッスンしている場合は、そのポートが Collector の送信ポートとして使用可能である必要があります。
RDP | TCP 3389 |

## <a name="collected-metadata"></a>収集されたメタデータ

> [!NOTE]
> Azure Migrate コレクター アプライアンスによって検出されたメタデータは、アプリケーションを Azure に移行し、Azure 適合性分析、アプリケーション依存関係分析、およびコスト計画を実行するときに、アプリケーションを適切なサイズにするのに役立ちます。 Microsoft では、ライセンスのコンプライアンス監査に関してこのデータを使用しません。

Collector アプライアンスでは、仮想マシンごとに次の構成メタデータが検出されます。 仮想マシンの構成データは、検出を開始してから 1 時間後に利用可能になります。

- (vCenter Server 上の) 仮想マシンの表示名
- 仮想マシンのインベントリ パス (vCenter Server 上のホストまたはフォルダー)
- IP アドレス
- MAC アドレス
- オペレーティング システム
- コア、ディスク、NIC の数
- メモリ サイズ、ディスク サイズ
- 仮想マシン、ディスク、およびネットワークのパフォーマンス カウンター。

### <a name="performance-counters"></a>パフォーマンス カウンター

 コレクター アプライアンスは 20 秒間隔で ESXi ホストから VM ごとに次のパフォーマンス カウンターを収集します。 これらのカウンターは vCenter カウンターであり、平均という用語が使われていますが、20 秒サンプルはリアルタイム カウンターです。 仮想マシンのパフォーマンス データは、検出の開始後、2 時間後にポータルで利用可能になります。 正確で適切なサイズ推奨を得るには、パフォーマンス ベースの評価を作成する前に、少なくとも 1 日待つことを強くお勧めします。 すぐに結果を見るには、サイジング条件を*オンプレミス*にして評価を作成する方法があります。オンプレミスの場合、正しいサイズの決定にパフォーマンス データが考慮されません。

**カウンター** |  **評価への影響**
--- | ---
cpu.usage.average | 推奨される VM サイズとコスト  
mem.usage.average | 推奨される VM サイズとコスト  
virtualDisk.read.average | ディスク サイズ、ストレージ コスト、仮想マシン サイズを計算します
virtualDisk.write.average | ディスク サイズ、ストレージ コスト、仮想マシン サイズを計算します
virtualDisk.numberReadAveraged.average | ディスク サイズ、ストレージ コスト、仮想マシン サイズを計算します
virtualDisk.numberWriteAveraged.average | ディスク サイズ、ストレージ コスト、仮想マシン サイズを計算します
net.received.average | VM のサイズを計算します                          
net.transmitted.average | VM のサイズを計算します     

Azure Migrate によって収集される VMware カウンターの完全なリストは、以下のとおりです。

**カテゴリ** |  **Metadata** | **vCenter データポイント**
--- | --- | ---
コンピューターの詳細 | VM ID | vm.Config.InstanceUuid
コンピューターの詳細 | VM 名 | vm.Config.Name
コンピューターの詳細 | vCenter Server ID | VMwareClient.InstanceUuid
コンピューターの詳細 |  VM の説明 |  vm.Summary.Config.Annotation
コンピューターの詳細 | ライセンス プロダクト名 | vm.Client.ServiceContent.About.LicenseProductName
コンピューターの詳細 | オペレーティング システムの種類 | vm.Summary.Config.GuestFullName
コンピューターの詳細 | オペレーティング システムのバージョン | vm.Summary.Config.GuestFullName
コンピューターの詳細 | ブートの種類 | vm.Config.Firmware
コンピューターの詳細 | コア数 | vm.Config.Hardware.NumCPU
コンピューターの詳細 | メモリのメガバイト数 | vm.Config.Hardware.MemoryMB
コンピューターの詳細 | ディスクの数 | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk).count
コンピューターの詳細 | ディスク サイズのリスト | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualDisk)
コンピューターの詳細 | ネットワーク アダプターのリスト | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualEthernetCard)
コンピューターの詳細 | CPU 使用率 | cpu.usage.average
コンピューターの詳細 | メモリ使用率 | mem.usage.average
ディスクの詳細 (ディスクごと) | ディスク キーの値 | disk.Key
ディスクの詳細 (ディスクごと) | ディスク ユニット数 | disk.UnitNumber
ディスクの詳細 (ディスクごと) | ディスク コントローラー キーの値 | disk.ControllerKey.Value
ディスクの詳細 (ディスクごと) | プロビジョニングされたギガバイト数 | virtualDisk.DeviceInfo.Summary
ディスクの詳細 (ディスクごと) | ディスク名 | この値は、disk.UnitNumber、disk.Key および disk.ControllerKey.Value を使用して生成されます
ディスクの詳細 (ディスクごと) | 1 秒あたりの読み取り操作の数 | virtualDisk.numberReadAveraged.average
ディスクの詳細 (ディスクごと) | 1 秒あたりの書き込み操作の数 | virtualDisk.numberWriteAveraged.average
ディスクの詳細 (ディスクごと) | 読み取りスループットの 1 秒あたりのメガバイト数 | virtualDisk.read.average
ディスクの詳細 (ディスクごと) | 書き込みスループットの 1 秒あたりのメガバイト数 | virtualDisk.write.average
ネットワーク アダプターの詳細 (NIC ごと) | ネットワーク アダプターの名前 | nic.Key
ネットワーク アダプターの詳細 (NIC ごと) | MAC アドレス | ((VirtualEthernetCard)nic).MacAddress
ネットワーク アダプターの詳細 (NIC ごと) | IPv4 アドレス | vm.Guest.Net
ネットワーク アダプターの詳細 (NIC ごと) | IPv6 アドレス | vm.Guest.Net
ネットワーク アダプターの詳細 (NIC ごと) | 読み取りスループットの 1 秒あたりのメガバイト数 | net.received.average
ネットワーク アダプターの詳細 (NIC ごと) | 書き込みスループットの 1 秒あたりのメガバイト数 | net.transmitted.average
インベントリ パスの詳細 | 名前 | container.GetType().Name
インベントリ パスの詳細 | 子オブジェクトの型 | container.ChildType
インベントリ パスの詳細 | 参照の詳細 | container.MoRef
インベントリ パスの詳細 | 完全なインベントリ パス | container.Name with complete path
インベントリ パスの詳細 | 親の詳細 | Container.Parent
インベントリ パスの詳細 | 各 VM のフォルダーの詳細 | ((Folder)container).ChildEntity.Type
インベントリ パスの詳細 | 各 VM フォルダーのデータセンターの詳細 | ((Datacenter)container).VmFolder
インベントリ パスの詳細 | 各ホスト フォルダーのデータセンターの詳細 | ((Datacenter)container).HostFolder
インベントリ パスの詳細 | 各ホストのクラスターの詳細 | ((ClusterComputeResource)container).Host)
インベントリ パスの詳細 | 各 VM のホストの詳細 | ((HostSystem)container).Vm


## <a name="securing-the-collector-appliance"></a>Collector アプライアンスのセキュリティ保護

次の手順に従って Collector アプライアンスをセキュリティで保護することをお勧めします

- 管理者パスワードは、承認されていないパーティとの間で、共有したり誤って付与したりしないでください。
- アプライアンスは、未使用時にはシャットダウンします。
- アプライアンスはセキュリティで保護されたネットワークに配置します。
- 移行が完了したら、アプライアンス インスタンスを削除します。
- さらに、移行後には、ディスクにはキャッシュされた vCenter 資格情報が存在する可能性があるため、ディスク バックアップ ファイル (VMDK) も必ず削除します。

## <a name="os-license-in-the-collector-vm"></a>Collector 仮想マシンの OS ライセンス

Collector には、180 日間有効な Windows Server 2012 R2 評価版ライセンスが付属します。 Collector 仮想マシンの評価期間が期限切れになった場合は、新しい OVA をダウンロードして、新しいアプライアンスを作成することをお勧めします。

## <a name="updating-the-os-of-the-collector-vm"></a>Collector 仮想マシンの OS の更新

Collector アプライアンスに 180 日間の評価版ライセンスがある場合でも、アプライアンスの自動シャットダウンを回避するために、アプライアンス上の OS は継続的に更新する必要があります。

- Collector が 60 日間更新されない場合には、マシンのシャットダウンが自動的に開始されます。
- 検出を実行中の場合は、60 日が経過しても、コンピューターはオフになりません。 検出ジョブが完了した後に、コンピューターはオフになります。
- 60 日が経過しても Collector を使用してきた場合には、Windows Update の実行によってマシンが常に更新され続けるようにすることをお勧めします。

## <a name="upgrading-the-collector-appliance-version"></a>Collector アプライアンス バージョンのアップグレード

OVA を再度ダウンロードせずに、Collector を最新バージョンにアップグレードできます。

1. [リストされている最新のアップグレード パッケージ](concepts-collector-upgrade.md)のダウンロード
2. ダウンロードした修正プログラムが安全であることを確認するために、管理者コマンド ウィンドウを開き、以下のコマンドを実行して、zip ファイルのハッシュを生成します。 生成されたハッシュは、その特定のバージョンのハッシュと一致する必要があります。

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (たとえば、C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256 のように使用します)
3. zip ファイルを Azure Migrate コレクター仮想マシン (コレクター アプライアンス) にコピーします。
4. zip ファイルを右クリックし、[すべて展開] を選択します。
5. Setup.ps1 を右クリックして [Run with PowerShell]\(PowerShell で実行\) を選択し、画面に指示に従って更新プログラムをインストールします。

## <a name="discovery-process"></a>検出プロセス

アプライアンスを設定した後は、検出を実行することができます。 そのしくみは次のとおりです。

- スコープによって検出を実行するとします。 指定した vCenter インベントリ パス内のすべての仮想マシンが検出されます。
    - 一度に 1 つのスコープを設定します。
    - スコープには仮想マシンを最大で 1,500 まで含めることができます。
    - スコープには、データ センター、フォルダー、または ESXi ホストのいずれかを指定できます。
- vCenter Server に接続したら、コレクションの移行プロジェクトを指定して接続します。
- 仮想マシンが検出され、そのメタデータとパフォーマンス データが Azure に送信されます。 これらのアクションは、コレクション ジョブの一部です。
    - Collector アプライアンスには、特定の Collector ID が付与されます。これは特定のマシンでの複数の検出で永続的に有効です。
    - 実行中のコレクション ジョブには特定のセッション ID が付与されます。 この ID はコレクション ジョブごとに変更され、トラブルシューティングに使用することができます。

## <a name="next-steps"></a>次の手順

[オンプレミスの VMware VM のアセスメントを設定する](tutorial-assessment-vmware.md)
