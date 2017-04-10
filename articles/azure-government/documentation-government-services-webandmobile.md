---
title: "Azure Government Web、モバイル、API | Microsoft Docs"
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: brendalee
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/5/2016
ms.author: brendal
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: f0ba3e50c4281a0e8c365be075ae45ab72f6e82d

---
# <a name="azure-government-web--mobile"></a>Azure Government Web とモバイル
## <a name="app-services"></a>アプリケーション サービス
### <a name="variations"></a>バリエーション
Azure App Service は、Azure Government で一般提供されています。

Azure Government で作成した Azure App Service アプリのアドレスは、パブリック クラウドで作成したアドレスとは異なります。

| サービスの種類 | Azure Public | Azure Government |
| --- | --- | --- |
| App Service |*.azurewebsites.net |*.azurewebsites.us|

パブリック クラウドで利用できる App Service の機能の一部は、Azure Government ではまだ利用できません。

- App Service Environment
- アプリのデプロイ
    - 継続的な配信 (プレビュー)
- Settings
    - VNET 統合とハイブリッド接続
    - セキュリティ スキャン
- 開発ツール
    - パフォーマンス テスト
    - リソース エクスプローラー
    - PHP のデバッグ
- 監視
    - Application Insights
    - インスタンスごとのメトリック
    - ライブ HTTP トラフィック
    - アプリケーション イベント
    - FREB ログ
- サポートとトラブルシューティング
    - App Service Advisor
    - エラー履歴
    - サービスとしての診断
    - 緩和する


### <a name="considerations"></a>考慮事項
以下の情報は、App Service に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
| --- | --- |
| Azure App Service 内で入力、保存、処理されるデータには、輸出規制対象データが含まれていてもかまいません。 Azure App Service で実行されるバイナリ。 静的な認証コード (Azure のプラットフォーム コンポーネントにアクセスするためのパスワード、スマートカードの PIN など)。 Azure のプラットフォーム コンポーネントを管理する際に使用する証明書の秘密キー。 SQL 接続文字列。 その他、Azure サービスに格納される各種セキュリティ情報/シークレット (証明書、暗号化キー、マスター キー、ストレージ キーなど)。 |メタデータに、輸出規制対象データを含めることは許可されません。 このメタデータには、Azure App Service を作成したり管理したりする際に入力するあらゆる構成データが含まれます。 リソース グループ、リソース名、リソース タグの各フィールドには規制対象データを入力しないでください。|

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、[Microsoft Azure Government ブログ](https://blogs.msdn.microsoft.com/azuregov/)をご覧ください。




<!--HONumber=Dec16_HO2-->


