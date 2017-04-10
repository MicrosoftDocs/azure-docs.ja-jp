| リソース | 無料 | Shared (プレビュー) | 基本 | Standard | Premium (プレビュー)</th> |
| --- | --- | --- | --- | --- | --- |
| [App Service プラン](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)あたりの [Web、モバイル、または API アプリ数](https://azure.microsoft.com/services/app-service/)<sup>1</sup> |10 |100 |無制限<sup>2</sup> |無制限<sup>2</sup> |無制限<sup>2</sup> |
| [App Service プラン](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)あたりの[ロジック アプリ数](https://azure.microsoft.com/services/app-service/logic/)</a><sup>1</sup> |10 |10 |10 |コアあたり 20 |コアあたり 20 |
| [[App Service プラン]](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |リージョンあたり 1 件 |リソース グループあたり 10 |リソース グループあたり 100 |リソース グループあたり 100 |リソース グループあたり 100 |
| コンピューティング インスタンスの種類 |共有 |共有 |専用<sup>3</sup> |専用<sup>3</sup> |専用<sup>3</sup></p> |
| [スケールアウト](../articles/app-service-web/web-sites-scale.md) (最大インスタンス) |1 Shared インスタンス |1 Shared インスタンス |3 専用インスタンス<sup>3</sup> |10 専用インスタンス<sup>3</sup> |20 専用インスタンス (ASE では50)<sup>3、4</sup> |
| ストレージ<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4、5</sup></p> |
| CPU 時間 (5 分)<sup>6</sup> |3 分 |3 分 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の料金で課金 |無制限、Standard の料金で課金 |
| CPU 時間 (1 日)<sup>6</sup> |約 60 分 |約 240 分 |無制限、Standard の[料金](https://azure.microsoft.com/pricing/details/app-service/)</a>で課金 |無制限、Standard の料金で課金 |無制限、Standard の料金で課金 |
| メモリ (1 時間) |App Service プランごとに 1,024 MB |アプリごとに 1,024 MB |該当なし |該当なし |該当なし |
| 帯域幅 |165 MB |無制限、 [データ転送の料金](https://azure.microsoft.com/pricing/details/data-transfers/) が適用される |無制限、データ転送の料金が適用される |無制限、データ転送の料金が適用される |無制限、データ転送の料金が適用される |
| アプリケーションのアーキテクチャ |32 ビット |32 ビット |32 ビット/64 ビット |32 ビット/64 ビット |32 ビット/64 ビット |
| インスタンスごとの Web ソケット数<sup>7</sup> |5 |35 |350 |無制限 |無制限 |
| アプリケーションごとの同時 [デバッガー接続](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) |1 |1 |1 |5 |5 |
| [FTP/S と SSL を利用する azurewebsites.net サブドメイン](../articles/app-service-web/web-sites-configure-ssl-certificate.md) |○ |○ |○ |○ |○ |
| [カスタム ドメイン](../articles/app-service-web/web-sites-custom-domain-name.md) のサポート | |○ |○ |○ |○ |
| カスタム ドメインの [SSL サポート](../articles/app-service-web/web-sites-configure-ssl-certificate.md) | | |無制限 |無制限、5 件の SNI SSL 接続と 1 件の IP SSL 接続が含まれる |無制限、5 件の SNI SSL 接続と 1 件の IP SSL 接続が含まれる |
| 統合 Load Balancer | |○ |○ |○ |○ |
| [常時接続](../articles/app-service-web/web-sites-configure.md) | | |○ |○ |○ |
| [スケジュールされたバックアップ](../articles/app-service-web/web-sites-backup.md) | | | |1 日に 1 回 |5 分ごとに 1 回<sup>8</sup> |
| [自動スケール](../articles/app-service-web/web-sites-scale.md) | | |○ |○ |○ |
| [WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md)<sup>9</sup> |○ |○ |○ |○ |○ |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) のサポート | |○ |○ |○ |○ |
| [エンドポイントの監視](../articles/app-service-web/web-sites-monitor.md) | | |○ |○ |○ |
| [ステージング スロット](../articles/app-service-web/web-sites-staged-publishing.md) | | | |5 |20 |
| アプリケーションごとのカスタム ドメイン数</a> | |500 |500 |500 |500 |
| SLA | |<p> |99.9% |99.95%<sup>10</sup> |99.95%<sup>10</sup> |

<sup>1</sup>アプリとストレージのクォータは、特に記述のない限り、App Service プラン単位の数字です。  
<sup>2</sup>これらのマシンで実際にホストできるアプリの数は、アプリのアクティビティ、マシン インスタンスのサイズ、対応するリソース使用量によって異なります。  
<sup>3</sup>専用インスタンスのサイズはさまざまです。 詳細については、「[App Service 料金](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/)」を参照してください。  
<sup>4</sup>Premium レベルでは、App Service 環境の使用時に最大 50 のコンピューティング インスタンス (可用性による) と 500 GB のディスク領域を、それ以外の場合に 20 のコンピューティング インスタンスと 250 GB のストレージを使用できます。  
<sup>5</sup>ストレージの制限は、同じ App Service プランのすべてのアプリにまたがる合計コンテンツ サイズです。 [App Service Environment](../articles/app-service-web/app-service-web-configure-an-app-service-environment.md#storage) ではより多くのストレージ オプションを利用できます。  
<sup>6</sup>これらのリソースは、専用インスタンス上の物理リソース (インスタンスのサイズとインスタンス数) によって制限されます。  
<sup>7</sup>Basic レベルでアプリケーションを 2 つのインスタンスにスケーリングする場合、2 つのインスタンスのそれぞれに 350 本の同時接続があります。  
<sup>8</sup>Premium レベルでは、バックアップの間隔を、App Service 環境の使用時に最小で 5 分ごとまで、それ以外の場合に 1 日 50 回まで下げることができます。  
<sup>9</sup>カスタムの実行可能ファイルやスクリプトを、オンデマンドやスケジュールで、または App Service インスタンス内のバックグラウンド タスクとして継続的に実行します。 Web ジョブを継続的に実行するには、常時接続が必要です。 スケジュールされた Web ジョブの実行には、Azure Scheduler Free または Standard が必要です。 App Service インスタンスで実行できる Web ジョブの数に定義済みの制限はありませんが、実際にはアプリケーション コードの処理に依存する限界があります。   
<sup>10</sup>フェールオーバー用に Azure Traffic Manager が構成された複数のインスタンスを使用するデプロイでは、SLA は 99.95% です。  



<!--HONumber=Feb17_HO3-->


