次の表は、IoT Hub Device Provisioning Service のリソースに適用される制限の一覧です。

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりの Device Provisioning Services の最大数 | 10 |
| 加入の最大数 | 10,000 |
| 登録の最大数 | 10,000 |
| 加入グループの最大数 | 100 |
| CA の最大数 | 10 |

> [!NOTE]
> これらの制限は、パブリック プレビューに対するものです。 サービスが一般公開になったら、[Microsoft サポート](https://azure.microsoft.com/support/options/)に連絡して、サブスクリプションのインスタンス数を増やすことができます。

Device Provisioning Service は、次のクォータを超えた場合に、要求を調整します。

| スロットル | サービスごとの値 |
| --- | --- |
| 操作 | 100/分 |
| デバイス登録 | 100/分 |
