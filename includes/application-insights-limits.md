アプリケーションごと (インストルメンテーション キーごと) のメトリックとイベントの数には制限があります。 制限は、選択する[料金プラン](https://azure.microsoft.com/pricing/details/application-insights/)によって異なります。

| **リソース** | **既定の制限** | **注**
| --- | --- | --- |
| 1 日あたりの合計データ量 | 500 GB | 上限を設定することでデータを削減できます。 さらに必要な場合は、AIDataCap@microsoft.com まで電子メールでご連絡ください。
| 1 月あたりの無料データ量<br/> (Basic 料金プラン) | 1 GB | 超過分については、GB 単位で課金されます。
| 調整 | 32,000 件のイベント/秒 | 制限は&1; 分以上にわたって測定されます。
| データの保持 | 90 日間 | このリソースは、[Search](../articles/application-insights/app-insights-diagnostic-search.md)、[Analytics](../articles/application-insights/app-insights-analytics.md)、および[メトリックス エクスプローラー](../articles/application-insights/app-insights-metrics-explorer.md)用です。
| [可用性の複数手順のテスト](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests)の詳細な結果の保持 | 90 日間 | このリソースは、各手順の詳細な結果を提供します。
| プロパティとメトリック名の長さ | 150 |
| プロパティ値の文字列の長さ | 8,192 |
| トレースおよび例外のメッセージ長 | 10 k |
| アプリあたりの[可用性テスト](../articles/application-insights/app-insights-monitor-web-app-availability.md)の数  | 10 |

詳細については、[Application Insights の価格とクォータ](../articles/application-insights/app-insights-pricing.md)に関するページを参照してください。


<!--HONumber=Feb17_HO2-->


