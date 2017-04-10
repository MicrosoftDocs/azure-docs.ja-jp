この記事では、スケーラビリティ、可用性、管理容易性、およびセキュリティに注目しながら、Azure で Linux 仮想マシン (VM) を実行するための実績のある一連の手法について説明します。 Azure は、CentOS、Debian、Red Hat Enterprise、Ubuntu、FreeBSD など、現在普及しているさまざまな Linux ディストリビューションに対応します。 詳細については、「[Azure と Linux][azure-linux]」を参照してください。

> [!NOTE]
> Azure には、[Resource Manager][resource-manager-overview] とクラシックという&2; 種類のデプロイメント モデルがあります。 この記事では、新しいデプロイ用に Microsoft から推奨されている Resource Manager を使います。
> 
> 

基幹業務ワークロードに単一の VM を使用すると単一障害点が生まれてしまうため、このような使用はお勧めしません。 可用性を高めるには、複数の VM を[可用性セット][availability-set]にデプロイします。 詳細については、[Azure での複数の VM の実行][multi-vm]に関するページをご覧ください。 

## <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム
Azure で VM をプロビジョニングする際は、VM 自体のみよりも、多くの変動的な部分があります。 コンピューティング、ネットワーク、およびストレージの要素を考慮する必要があります。

> 次のアーキテクチャ ダイアグラムを含む Visio ドキュメントは、[Microsoft ダウンロード センター][visio-download]からダウンロードできます。 このダイアグラムは、コンピューティングの単一 VM に関するページに掲載されています。
> 
> 

![[0]][0]

* **リソース グループ。** [*リソース グループ*][resource-manager-overview]は、関連リソースを保持するコンテナーです。 この VM のリソースを保持するリソース グループを作成します。
* **VM**。 VM は、発行されたイメージの一覧、または Azure BLOB ストレージにアップロードした仮想ハード ディスク (VHD) ファイルからプロビジョニングできます。
* **OS ディスク。** OS ディスクは、[Azure Storage][azure-storage] に格納されている VHD です。 これは、ホスト コンピューターがダウンした場合でも VM が保持されることを意味します。 OS ディスクは `/dev/sda1`です。
* **一時ディスク。** VM は一時ディスクを使用して作成されます。 このディスクは、ホスト コンピューターの物理ドライブ上に格納されます。 Azure Storage には保存 "*されない*" ため、再起動中や他の VM ライフサイクル イベント時に削除される可能性があります。 ページ ファイルやスワップ ファイルなどの一時的なデータにのみ、このディスクを使用してください。 一時ディスクは `/dev/sdb1` で、`/mnt/resource` または `/mnt` でマウントされます。
* **データ ディスク。** [データ ディスク][data-disk]は、アプリケーション データ用の永続的な VHD です。 データ ディスクは、OS ディスクと同様に、Azure Storage に格納されます。
* **仮想ネットワーク (VNet) とサブネット。** Azure 上のすべての VM は、さらに複数のサブネットに分かれる VNet にデプロイされます。
* **パブリック IP アドレス。** パブリック IP アドレスは、SSH 経由など、VM と通信するために必要です。
* **ネットワーク インターフェイス (NIC)**。 NIC を使用すると、VM は仮想ネットワークと通信できます。
* **ネットワーク セキュリティ グループ (NSG)**。 [NSG][nsg] は、サブネットへのネットワーク トラフィックを許可または拒否するために使用します。 NSG は、個々 の NIC またはサブネットに関連付けることができます。 NSG をサブネットに関連付けると、そのサブネット内のすべての VM に NSG ルールが適用されます。
* **[診断]。** 診断ログは、VM の管理とトラブルシューティングにとって非常に重要です。

## <a name="recommendations"></a>推奨事項

ほとんどのシナリオには、次の推奨事項が適用されます。 これらの推奨事項には、優先される特定の要件がない限り、従ってください。 

### <a name="vm-recommendations"></a>VM の推奨事項

Azure にはさまざまな仮想マシン サイズが用意されていますが、DS シリーズと GS シリーズをお勧めします。これらのマシン サイズでは [Premium Storage][premium-storage] がサポートされるためです。 ハイパフォーマンス コンピューティングなどの特殊なワークロードがない限り、これらのマシン サイズのいずれかを選択してください。 詳細については、[仮想マシンのサイズ][virtual-machine-sizes]に関するページをご覧ください。

既存のワークロードを Azure に移動する場合は、オンプレミスのサーバーに最も適合性が高い VM サイズから開始します。 次に、CPU、メモリ、およびディスクの&1; 秒あたりの入力/出力操作 (IOPS) について、実際のワークロードのパフォーマンスを測定し、必要に応じてサイズを調整します。 VM 用に複数の NIC が必要な場合は、NIC の最大数が [VM のサイズ][vm-size-tables]と関係することに注意してください。

VM および他のリソースをプロビジョニングする際は、リージョンを指定する必要があります。 一般的に、内部ユーザーや顧客に最も近いリージョンを選択します。 ただし、すべてのリージョンですべての VM サイズを利用できるとは限りません。 詳細については、[リージョン別のサービス][services-by-region]に関するページを参照してください。 指定したリージョンで利用できる VM サイズを一覧表示するには、次の Azure コマンド ライン インターフェイス (CLI) コマンドを実行します。

```
azure vm sizes --location <location>
```

発行された VM イメージの選択については、「[Azure CLI を使用した Linux VM イメージの選択][select-vm-image]」を参照してください。

### <a name="disk-and-storage-recommendations"></a>ディスクとストレージの推奨事項

最適なディスク I/O パフォーマンスを得るには、データがソリッド ステート ドライブ (SSD) に格納される [Premium Storage][premium-storage] をお勧めします。 コストは、プロビジョニングされたディスクのサイズに基づいて決まります。 また、IOPS とスループット (つまり、データ転送速度) もディスク サイズによって異なるため、ディスクをプロビジョニングする場合は、3 つの要素 (容量、IOPS、スループット) すべてを考慮してください。 

ストレージ アカウントの IOPS 制限に達するのを回避するために、仮想ハード ディスク (VHD) を保持する Azure ストレージ アカウントを VM ごとに個別に作成します。 

1 つ以上のデータ ディスクを追加します。 作成した VHD は、フォーマットされていません。 その VM にログインしてディスクをフォーマットしてください。 Linux のシェルでは、データ ディスクは `/dev/sdc`、`/dev/sdd` などのように表示されます。 `lsblk` を実行すると、ディスクなどのブロック デバイスの一覧を表示できます。 データ ディスクを使用するには、パーティションとファイル システムを作成し、ディスクをマウントします。 次に例を示します。

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

データ ディスクの数が多い場合は、ストレージ アカウントの合計 I/O 制限に注意してください。 詳細については、「[仮想マシン ディスクの制限][vm-disk-limits]」を参照してください。

データ ディスクを追加すると、ディスクに論理ユニット番号 (LUN) の ID が割り当てられます。 LUN ID は必要に応じて指定できます。たとえば、ディスクを交換する際に同じ LUN ID を保持したい場合や、特定の LUN ID を検索するアプリケーションがある場合などに指定します。 ただし、ディスクごとに一意な LUN ID である必要があります。

Premium Storage アカウントで使用する VM のディスクは SSD なので、I/O スケジューラを変更して、SSD のパフォーマンスを最適化することができます。 一般的な推奨事項は、SSD に対して NOOP スケジューラを使用することですが、 [iostat] などのツールを使用して、特定のワークロードのディスク I/O パフォーマンスを監視する必要があります。

最適なパフォーマンスを得るには、診断ログを保持するためのストレージ アカウントを別途作成します。 診断ログには、標準的なローカル冗長ストレージ (LRS) アカウントがあれば十分です。

### <a name="network-recommendations"></a>ネットワークの推奨事項

パブリック IP アドレスは、動的でも静的でもかまいません。 既定では、動的アドレスになっています。

* 変化しない固定 IP アドレスが必要な場合 (たとえば、DNS に A レコードを作成する必要がある場合や IP アドレスをホワイトリストに登録する必要がある場合) は、[静的 IP アドレス][static-ip]を予約します。
* IP アドレスの完全修飾ドメイン名 (FQDN) を作成することもできます。 これにより、その FQDN を参照する DNS で [CNAME レコード][cname-record]を登録できます。 詳細については、「[Azure Portal での完全修飾ドメイン名の作成][fqdn]」を参照してください。

すべての NSG に[既定の規則][nsg-default-rules] (すべての受信インターネット トラフィックをブロックする規則など) のセットが含まれています。 既定のルールを削除することはできませんが、他の規則でオーバーライドすることはできます。 インターネット トラフィックを有効にするには、特定のポート (HTTP のポート 80 など) への着信トラフィックを許可するルールを作成します。  

SSH を有効にするには、TCP ポート 22 への着信トラフィックを許可する規則を NSG に追加します。

## <a name="scalability-considerations"></a>拡張性に関する考慮事項

スケールアップまたはスケールダウンするには、[VM サイズを変更します][vm-resize]。 

水平方向にスケール アウトするには、ロード バランサーの内側の可用性セットに&2; つ以上の VM を配置します。 詳細については、[Azure での複数の VM の実行][multi-vm]に関するページを参照してください。

## <a name="availability-considerations"></a>可用性に関する考慮事項

可用性を高めるには、複数の VM を可用性セットにデプロイします。 こうすることにより、[サービス レベル アグリーメント][vm-sla] (SLA) も高くなります。 

VM は、[計画的メンテナンス][planned-maintenance]または[計画外メンテナンス][manage-vm-availability]の影響を受ける可能性があります。 [VM の再起動ログ][reboot-logs]を参照すると、VM の再起動が計画的なメンテナンスによるものかどうかを確認できます。

VHD は [Azure Storage][azure-storage] に格納され、Azure Storage は持続性と可用性を確保するためにレプリケートされます。

通常の操作中に (ユーザー エラーなどによる) 偶発的なデータの損失から保護するために、[BLOB スナップショット][blob-snapshot]や他のツールを使用してポイントインタイム バックアップを実装することも必要です。

## <a name="manageability-considerations"></a>管理容易性に関する考慮事項

**リソース グループ** 同じライフ サイクルを共有する密結合のリソースを同じ[リソース グループ][resource-manager-overview]に配置します。 リソース グループを使用すると、グループとしてリソースをデプロイおよび監視し、リソース グループ別に請求コストをまとめることができます。 セットとしてリソースを削除することもできます。これはテスト デプロイの場合に便利です。 リソースにはわかりやすい名前を付けます。 これにより、特定のリソースを見つけて、その役割を理解することが簡単になります。 「[Recommended Naming Conventions for Azure Resources][naming conventions]」(Azure リソースの推奨される名前付け規則) を参照してください。

**SSH**。 Linux VM を作成する前に、2048 ビット RSA 公開/秘密キー ペアを生成します。 VM を作成する場合は、公開キー ファイルを使用します。 詳細については、[Azure 上の Linux または Mac における SSH の使用方法][ssh-linux]に関する記事を参照してください。

**VM 診断。** 基本的な正常性メトリック、診断インフラストラクチャ ログ、[ブート診断][boot-diagnostics]などの監視と診断を有効にします。 VM が起動不可能な状態になった場合は、起動エラーを診断するのにブート診断が役立ちます。 詳細については、「[監視と診断の有効化][enable-monitoring]」を参照してください。  

次の CLI コマンドを実行して、診断を有効にします。

```
azure vm enable-diag <resource-group> <vm-name>
```

**VM の停止。** Azure では、"停止" 状態と "割り当て解除済み" 状態が区別されます。 VM が割り当て解除されたときではなく、VM が停止状態のときに課金されます。

次の CLI コマンドを使用して、VM の割り当てを解除します。

```
azure vm deallocate <resource-group> <vm-name>
```

Azure Portal の **[停止]** ボタンを使用すると、VM の割り当てが解除されます。 ただし、ログイン中に OS からシャットダウンした場合、VM は停止しますが、割り当て解除されない ** ため、引き続き課金されます。

**VM の削除。** VM を削除しても VHD は削除されません。 つまり、データを失うことなく安全に VM を削除できます。 ただし、Storage に対して引き続き課金されます。 VHD を削除するには、[Blob Storage][blob-storage] からファイルを削除します。

誤って削除されないように、[リソース ロック][resource-lock]を使用してリソース グループ全体をロックするか、VM などの個々のリソースをロックします。 

## <a name="security-considerations"></a>セキュリティに関する考慮事項

[OSPatching] VM 拡張機能を使用して、OS の更新を自動化します。 VM をプロビジョニングする場合は、この拡張機能をインストールしてください。 パッチをインストールする頻度と、パッチの適用後に再起動するかどうかを指定することができます。

[ロールベースのアクセス制御][rbac] (RBAC) を使用して、デプロイする Azure リソースへのアクセスを制御します。 RBAC を使用すると、DevOps チームのメンバーに承認の役割を割り当てることができます。 たとえば、閲覧者の役割では、Azure リソースを表示することはできますが、作成、削除、または管理することはできません。 一部の役割は、特定の Azure リソースの種類に固有です。 たとえば、仮想マシン共同作成者ロールでは、VM の再起動または割り当て解除、管理者パスワードのリセット、VM の作成などができます。 このリファレンス アーキテクチャで役立つ他の[組み込みの RBAC の役割][rbac-roles]には、[DevTest Labs ユーザー][rbac-devtest]や[ネットワーク共同作成者][rbac-network]などがあります。 

ユーザーを複数の役割に割り当てることができ、よりきめ細かいアクセス許可のカスタム ロールを作成することができます。

d> [!NOTE]
> RBAC では、VM にログインしているユーザーが実行できる操作は制限されません。 これらのアクセス許可は、ゲスト OS のアカウントの種類によって決まります。   
> 
> 

プロビジョニング操作や他の VM イベントを確認するには、[監査ログ][audit-logs]を使用します。

OS ディスクとデータ ディスクを暗号化する必要がある場合は、[Azure Disk Encryption][disk-encryption] を検討します。 

## <a name="solution-deployment"></a>ソリューションのデプロイ
このリファレンス アーキテクチャのデプロイについては、[GitHub][github-folder] を参照してください。 このアーキテクチャには、VNet、NSG、および&1; つの VM が含まれています。 このアーキテクチャをデプロイするには、次の手順を実行します。 

1. 下のボタンを右クリックし、[リンクを新しいタブで開く] または [リンクを新しいウィンドウで開く] を選択します。
   [![Azure へのデプロイ](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Azure Portal でリンクが開いたら、いくつかの設定に値を入力する必要があります。 
   
   * **リソース グループ**の名前はパラメーター ファイルで既に定義されているため、**[新規作成]** を選択し、テキスト ボックスに「`ra-single-vm-rg`」と入力します。
   * **[場所]** ボックスの一覧でリージョンを選択します。
   * **[Template Root Uri (テンプレート ルート URI)]** または **[Parameter Root Uri (パラメーター ルート URI)]** ボックスは編集しないでください。
に関するページを参照してください。   * **[OS の種類]** ボックスの一覧から **Linux** を選択します。
   * 使用条件を確認し、**[上記の使用条件に同意する]** チェック ボックスをオンにします。
   * **[購入]** をクリックします。
3. デプロイが完了するまで待ちます。
4. パラメーター ファイルには、ハードコーディングされた管理者のユーザー名とパスワードが含まれているため、この両方をすぐに変更することを強くお勧めします。 Azure Portal で `ra-single-vm0 ` という名前の VM をクリックします。 次に、**[サポート + トラブルシューティング]** セクションの **[パスワードのリセット]** をクリックします。 **[モード]** ボックスの一覧の **[パスワードのリセット]** を選択し、新しい**ユーザー名**と**パスワード**を選択します。 **[更新]** をクリックして、新しいユーザー名とパスワードを保持します。

## <a name="next-steps"></a>次のステップ
高可用性を実現するために、ロード バランサーの背後に VM を&2; つ以上デプロイします。 詳細については、[Azure での複数の VM の実行][multi-vm]に関するページをご覧ください。

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/storage/storage-about-disks-and-vhds-linux.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Azure における単一の Linux VM アーキテクチャ"

