| **ハードウェア** | |
| --- |---|
| CPU コアの数| 8 |
| RAM| 12 GB|
| ディスクの数 | 3 <br><br> - OS ディスク<br> - プロセス サーバー キャッシュ ディスク<br> - リテンション ドライブ (フェールバック用)|
| ディスクの空き領域 (プロセス サーバー キャッシュ) | 600 GB
| ディスクの空き領域 (リテンション ディスク) | 600 GB|
| **ソフトウェア** | |
| オペレーティング システムのバージョン | Windows Server 2012 R2 |
| オペレーティング システムのロケール | 英語 (en-us)|
| VMware vSphere PowerCLI のバージョン | [PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0 "PowerCLI 6.0")|
| Windows Server の役割 | 次の役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V |
| **ネットワーク** | |
| ネットワーク インターフェイス カードの種類 | VMXNET3 |
| IP アドレスの種類 | 静的 |
| インターネットへのアクセス | サーバーは、直接またはプロキシ サーバーを介して次の URL にアクセスできる必要があります。 <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (スケールアウト プロセス サーバーの場合は不要) <br> - time.nist.gov <br> - time.windows.com |
| ポート | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送)|
