---
title: "同期での Azure AD Connect Health の使用 | Microsoft Docs"
description: "Azure AD Connect 同期を監視する方法を説明する Azure AD Connect Health のページです。"
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2017
ms.author: vakarand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b9a3b64d9de48f17a295ca7a9ea58cf26e8f83ed
ms.openlocfilehash: f5d5f238f8f57c8dcf7a3759274f9d565e73ecce
ms.lasthandoff: 02/28/2017

---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Azure AD Connect Health を使用した Azure AD Connect の同期の監視
次のドキュメントは、Azure AD Connect Health を使用した Azure AD Connect (同期) の監視について記述しています。  Azure AD Connect Health を使用して AD FS を監視する方法の詳細については、「 [AD FS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adfs.md)」を参照してください。 また、Azure AD Connect Health での Active Directory ドメイン サービスの監視については、「 [AD DS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adds.md)」を参照してください。

![Azure AD Connect Health for Sync](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Azure AD Connect Health for Sync のアラート
Azure AD Connect Health for Sync アラート セクションには、アクティブなアラートの一覧が表示されます。 各アラートには、関連情報、解決の手順、関連ドキュメントのリンクが含まれます。 アクティブまたは解決済みのアラートを選択すると、新しいブレードが開き、アラートの解決に利用できる手順やその他のドキュメントへのリンクなどの追加情報が表示されます。 過去に解決されたアラートの履歴データも表示できます。

アラートを選択すると、アラートの解決に利用できる手順やその他のドキュメントへのリンクなどの追加情報が表示されます。

![Azure AD Connect sync error](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>アラートの評価が限定される状況
Azure AD Connect で既定の構成が使用されていない場合 (たとえば、属性フィルターが既定の構成からカスタム構成に変更されている場合)、Azure AD Connect Health エージェントは Azure AD Connect に関連するエラー イベントをアップロードしません。

その結果、サービスによるアラートの評価は限定的なものになります。 このような状況にある場合は、それを示すバナーが該当するサービスの Azure ポータルに表示されます。

![Azure AD Connect Health for Sync](./media/active-directory-aadconnect-health-sync/banner.png)

この状況を変更するには、[設定] をクリックし、Azure AD Connect Health エージェントがすべてのエラー ログをアップロードできるようにします。

![Azure AD Connect Health for Sync](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>同期に関する洞察
管理者は、Azure AD に対する変更の同期にかかった時間と発生する変更の量を日常的に把握する必要があります。 この機能を利用すると、以下のグラフを使用してこれを簡単に視覚化できます。   

* 同期処理の遅延
* オブジェクト変更傾向

### <a name="sync-latency"></a>同期の遅延
この機能は、コネクタの同期処理 (インポート、エクスポートなど) に伴う遅延の傾向をグラフィカルに表示します。  処理の遅延をすばやく簡単に把握できることに加え (変更が大量に発生している場合などに有効)、待機時間が異常に長く調査が必要なケースを検出することができます。

![同期の遅延](./media/active-directory-aadconnect-health-sync/synclatency02.png)

既定では、Azure AD コネクタの "エクスポート" 処理の遅延のみ表示されます。  コネクタに関してそれ以外の処理を確認したり、他のコネクタからの処理を確認したりするには、グラフを右クリックし、[グラフの編集] を選択するか、[遅延グラフの編集] ボタンをクリックして具体的な処理とコネクタを選択してください。

### <a name="sync-object-changes"></a>同期オブジェクトの変更
評価後 Azure AD にエクスポートされている変更の数の傾向は、この機能によってグラフィカルに表示することができます。  現在、同期ログからこの情報を収集することは困難です。  このグラフを見れば、ご利用の環境内で生じている変更の数を簡単に監視できるだけでなく、発生しているエラーを視覚的に確認することができます。

![同期の遅延](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>オブジェクト レベルの同期エラー レポート (プレビュー)
Windows Server AD と Azure AD との間で Azure AD Connect を使って ID データを同期するときに発生する同期エラーについてのレポートは、この機能で入手できます。

* このレポートには、同期クライアント (Azure AD Connect Version 1.1.281.0 以降) によって記録されたエラーが含まれます。
* 同期エンジンにおける直近の同期操作で発生したエラーが対象となります  (Azure AD Connector の "エクスポート")。
* レポートに最新のデータを取り込むには、Azure AD Connect Health の同期エージェントに、適切なエンド ポイントへの送信接続が必要です。
* このレポートは、Azure AD Connect Health の同期エージェントによってアップロードされたデータを使用して **30 分ごとに更新**されます。
  その主な機能を示します。

  * エラー カテゴリの分類
  * オブジェクトとエラー (カテゴリごと) の一覧表示
  * エラーについてのすべてのデータを一元化
  * 競合エラーのあるオブジェクトを並べて比較
  * エラー レポートを CVS としてダウンロード (近日公開予定)

### <a name="categorization-of-errors"></a>エラー カテゴリの分類
レポートでは、既存の同期エラーが次のカテゴリで分類されます。

| カテゴリ | Description |
| --- | --- |
| 重複する属性 |Azure AD において本来テナントごとに一意であるべき属性 (proxyAddresses、UserPrincipalName など) の値に重複があるオブジェクトを Azure AD Connect が作成または更新しようとしたときに発生するエラー。 |
| 一致しないデータ |あいまいな一致の処理で、同期エラーを引き起こしたオブジェクトの突き合わせに失敗したときのエラー。 |
| データ検証の失敗 |無効なデータに起因するエラー。たとえばサポート対象外の文字が重要な属性 (UserPrincipalName など) に使用されていたり、データの形式に誤りがあるために Azure AD への書き込み前の検証に失敗したりしたケースが対象となります。 |
| 大きい属性 |許可されているサイズや長さ、数を超える属性がある場合に発生するエラー。 |
| その他 |上記のカテゴリに該当しないその他すべてのエラー。 このカテゴリは、フィードバックに基づいてさらに細かく分類されます。 |

![同期エラー レポートの概要](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![同期エラー レポートのカテゴリ](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>オブジェクトとエラー (カテゴリごと) の一覧表示
それぞれのカテゴリを掘り下げていくと、そのカテゴリのエラーに該当する一連のオブジェクトが表示されます。
![同期エラー レポートの一覧](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Error Details
エラーごとの詳しい画面で以下のデータを確認できます。

* 関連する "*AD オブジェクト*" の識別子
* 関連する "*Azure AD オブジェクト*" の識別子 (該当する場合)
* エラーの説明と解決方法
* 関連記事

![同期エラー レポートの詳細](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>エラー レポートを CSV としてダウンロード
[エクスポート] ボタンを選択すると、すべてのエラーに関する詳細情報を含んだ CSV ファイルをダウンロードできます。

## <a name="related-links"></a>関連リンク
* [同期中のエラーのトラブルシューティング](../connect/active-directory-aadconnect-troubleshoot-sync-errors.md)
* [重複属性の回復性](../connect/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health エージェントのインストール](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health の操作](active-directory-aadconnect-health-operations.md)
* [AD FS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adfs.md)
* [AD DS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health の FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health のバージョンの履歴](active-directory-aadconnect-health-version-history.md)
