---
title: Azure AD Connect 同期 V2 エンドポイント | Microsoft Docs
description: このドキュメントでは、Azure AD Connect 同期 V2 エンドポイント API の更新について説明します。
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ecfd277f2cc86102d59b201e7b43fa8519bdd3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98937602"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Azure AD Connect 同期 V2 エンドポイント API 
Microsoft がデプロイした Azure AD Connect 用の新しいエンドポイント (API) では、Azure Active Directory に対する同期サービス操作のパフォーマンスが向上しています。 新しい V2 エンドポイントを利用すると、Azure AD に対するエクスポートとインポートのパフォーマンスが明らかに向上していることがわかるでしょう。 この新しいエンドポイントでは、次のものがサポートされています。
    
 - メンバー数が最大 250,000 までのグループの同期
 - Azure AD に対するエクスポートとインポートのパフォーマンスの向上
 
> [!NOTE]
> 現在、新しいエンドポイントでは、書き戻される Microsoft 365 グループに対するグループ サイズの制限は構成されていません。 これにより、Active Directory と同期サイクルの待機時間に影響がある可能性があります。 グループ サイズは段階的に増やすことをお勧めします。  

## <a name="prerequisites"></a>前提条件  
新しい V2 エンドポイントを使用するには、[Azure AD Connect バージョン 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 以降を使用し、以下で説明するデプロイ手順に従って、Azure AD Connect サーバーに対して V2 エンドポイントを有効にする必要があります。   

## <a name="deployment-guidance"></a>展開のガイダンス 
V2 エンドポイントを使用するには、[Azure AD Connect バージョン 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 以降をデプロイする必要があります。 提供されているリンクを使用してダウンロードしてください。 

環境内に新しいエンドポイントをロールアウトするには、[スウィング移行](./how-to-upgrade-previous-version.md#swing-migration)方法を使用することをお勧めします。 これにより、大きなロールバックが必要な場合に対する明確なコンティンジェンシー計画が提供されます。 次の例では、このシナリオでスウィング移行を使用する方法を示します。 スウィング移行のデプロイ方法の詳細については、提供されているリンクを参照してください。 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>V2 エンドポイントをデプロイするためのスウィング移行
次の手順では、スウィング方法を使用して V2 エンドポイントをデプロイする方法を示します。

1. 現在のステージング サーバーに V2 エンドポイントをデプロイします。 以下の手順では、このサーバーを **V2 サーバー** と呼びます。 現在アクティブなサーバーでは、V1 エンドポイントを使用して運用ワークロードの処理が続けられます。以下ではこれを **V1 サーバー** と呼びます。
1. **V2 サーバー** でまだ想定どおりにインポートの処理が行われていることを確認します。 このステージでは、大規模なグループは Azure AD またはオンプレミスの AD に対してプロビジョニングされませんが、アップグレードによって既存の同期プロセスに予期しない影響が生じないことを確認できます。 
2. 検証が完了したら、**V2 サーバー** をアクティブなサーバーに切り替え、**V1 サーバー** をステージング サーバーに切り替えます。 この時点では、同期対象の範囲内にある大規模なグループが、Azure AD に対してプロビジョニングされます。また、グループの書き戻しが有効になっている場合は、大規模な Microsoft 365 統合グループが AD に対してプロビジョニングされます。
3. **V2 サーバー** によって大規模なグループが正常に実行および処理されていることを検証します。 このステップでいったん止めて、しばらく同期プロセスを監視することもできます。
  >[!NOTE]
  > 前の構成に戻す必要がある場合は、**V2 サーバー** から **V1 サーバー** に戻すスウィング移行を実行できます。 V1 エンドポイントでは 50,000 メンバーを超えるグループはサポートされていないため、Azure AD Connect によって Azure AD またはオンプレミスの AD にプロビジョニングされた大規模なグループは、その後削除されます。 
4. V2 エンドポイントの使用に問題がなければ、**V1 サーバー** をアップグレードして、V2 エンドポイントの使用を開始します。 
 

## <a name="expectations-of-performance-impact"></a>予想されるパフォーマンスへの影響  
V2 エンドポイントを使用した場合、パフォーマンスの向上は、同期されるグループの数、グループのサイズ、グループのチャーン (グループのメンバーとしてユーザーを追加および削除した結果のアクティビティ) に応じて変化します。 同期されるグループの数、サイズ、またはチャーンを増やさずに新しいエンドポイントを使用すると、Azure AD のエクスポートとインポートの時間が短縮されます。 
 
一方、大規模なグループを同期するときは、必要な追加処理によってパフォーマンスの向上が打ち消される可能性があります。 同期プロセスに追加する大規模なグループの数が多すぎると、全体的な同期時間が長くなる可能性さえあります。  

新しいグループの追加が同期パフォーマンスにどのように影響するかについて理解を深めるには、最初は、メンバーが 100,000 未満の少数の大規模グループのみを同期することをお勧めします。 その後、OU、属性、または最大グループ サイズのフィルター処理によって、範囲に含めるグループの数やサイズを増やすことができます。 パフォーマンスが向上するのは、Azure AD コネクタでのエクスポート タスクとインポート タスクであり、オンプレミスの AD コネクタでは向上しません。 

## <a name="deployment-step-by-step"></a>デプロイの手順 
新しい V2 エンドポイントをデプロイする 3 つのフェーズの詳細な例を次に示します。  実際のデプロイに対するガイドラインとして、これらのフェーズを使用してください。

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>フェーズ 1 – Azure AD Connect をインストールして検証する 
最初に [Azure AD Connect バージョン 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 以降をインストールまたはアップグレードし、同期プロセスを検証する手順を行ってから、V2 エンドポイントを有効にする第 2 フェーズに進むことをお勧めします。 Azure AD Connect サーバーで次のようにします。 


1. [省略可能] データベースのバックアップを行います 
2. [Azure AD Connect バージョン 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 以降をインストールするか、それにアップグレードします。
3. インストールを検証する 

### <a name="phase-2--enable-the-v2-endpoint"></a>フェーズ 2 – V2 エンドポイントを有効にする 
次のステップでは、V2 エンドポイントを有効にします。 

> [!NOTE]
> サーバーで V2 エンドポイントを有効にすると、既存のワークロードのパフォーマンスが若干向上することがわかります。 ただし、メンバー数が 50K を超えるグループはまだ同期できません。 

V2 エンドポイントに切り替えるには、次の手順のようにします。 

1. 管理者として PowerShell プロンプトを開きます。 
2. 同期操作が実行されていないことを確認した後、同期スケジューラを無効にします。 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. この新しいモジュールをインポートします。 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  V2 エンドポイントに切り替えます。

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
これで、サーバーに対して V2 エンドポイントが有効になりました。 V2 エンドポイントを有効にした後、いくらか時間をかけて予期しない結果が発生しないことを確認してから、次のフェーズに進み、グループ サイズの上限を引き上げます。 
>[!NOTE]
>Azure AD Connect のインストール時に指定したインストール パスによっては、ファイルやモジュールのパスで別のドライブ文字が使用されている場合があります。 


### <a name="phase-3--increase-the-group-membership-limit"></a>フェーズ 3 – グループ メンバーシップの制限を増やす 
予期しない結果が発生しないでサービスが実行されることを確認したら、グループ メンバーシップの上限の引き上げに進むことができます。 最初に、メンバーシップの上限を少し大きい値に上げて (75,000 メンバーなど)、大規模なグループが Azure AD に同期されるのを確認することをお勧めします。 結果に問題がなければ、メンバーの上限をさらに上げることができます。  

上限は、グループあたり 250,000 メンバーです。 

次の手順のようにして、メンバーシップの上限を増やすことができます。  

1. Azure AD 同期規則エディターを開きます 
2. エディターで、[方向] として **[送信]** を選択します 
3. **[Out to AAD – Group Join]\(AAD への送信 – グループへの参加\)** 同期ルールをクリックします 
4. **[編集]** ボタンをクリックします。 ![[Out to AAD – Group Join]\(AAD への送信 – グループへの参加\) が選択されている [View and manage your synchronization rules]\(同期ルールの表示と管理\) を示すスクリーンショット。](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. **[はい]** ボタンをクリックして既定のルールを無効にし、編集可能なコピーを作成します。
 ![[はい] ボタンが選択されている [Edit Reserved Rule Confirmation]\(予約済みの規則の編集の確認\) ウィンドウを示すスクリーンショット。](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. **[説明]** ページのポップアップ ウィンドウで、優先順位を 1 から 99 の範囲の使用可能な値に設定します。 ![[優先順位] が強調表示されている [Edit outbound synchronization rule]\(送信同期規則の編集\) ウィンドウを示すスクリーンショット。](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. **[変換]** ページで、**member** の変換に対する **[ソース]** の値を更新し、"50000" を 50001 から 250000 の範囲の値に置き換えます。 この置換により、Azure AD に同期されるグループのメンバーシップの最大サイズが増加します。 最初は 100,000 メンバーにして、大規模なグループを同期したときの同期のパフォーマンスに対する影響を確認することをお勧めします。 
 
 **例** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![同期ルールを編集する](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. [保存] をクリックします。 
10. 管理者として PowerShell プロンプトを開きます 
11. 同期スケジューラを再度有効にします 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Azure AD Connect Health が有効になっていない場合は、ログを上書きするのではなく、Windows アプリケーションのイベント ログの設定を変更してログをアーカイブします。 ログは、後のトラブルシューティング作業で役に立つ場合があります。 

>[!NOTE]
> 新しいエンドポイントを有効にした後、AAD コネクタで "dn-attributes-failure" という名前のエクスポート エラーが新しく発生する場合があります。 ID 6949 のエラーごとに、対応するイベント ログ エントリがあります。 これらのエラーは情報であり、インストールに関する問題を示すのではなく、メンバー オブジェクト自体が Azure AD に同期されなかったため、同期プロセスで Azure AD のグループに特定のメンバーを追加できなかったことを示します。 

新しい V2 エンドポイントのコードでは、一部の種類のエクスポート エラーを処理する方法が、V1 のコードと若干異なります。  V2 エンドポイントを使用すると、より多くの情報エラー メッセージが表示される場合があります。 

>[!NOTE]
> Azure AD Connect をアップグレードする場合は、変更がアップグレード プロセスで保持されないため、フェーズ 2 の手順を確実に再度実行してください。 

これ以降に、 **[Out to AAD – Group Join]\(AAD への送信 – グループへの参加\)** 同期ルールでグループ メンバーの制限を増やすときは、完全同期を行う必要はないので、PowerShell で次のコマンドを実行して、完全同期を抑制してもかまいません。 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> メンバー数が 50,000 を超える Microsoft 365 統合グループがある場合は、グループが Azure AD Connect に読み込まれ、グループの書き戻しが有効になっていると、オンプレミスの AD に書き込まれます。 

## <a name="rollback"></a>ロールバック 
V2 エンドポイントを有効にしてあり、ロールバックを行う必要がある場合は、次の手順のようにします。 

1. Azure AD Connect サーバーで次のようにします: a. [省略可能] データベースのバックアップを行います 
2. 管理者として PowerShell プロンプトを開きます
3. 同期操作が実行されていないことを確認した後、同期スケジューラを無効にします
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. V1 エンドポイントに切り替えます * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Azure AD 同期規則エディターを開きます 
6. **[Out to AAD – Group Join]\(AAD への送信 – グループへの参加\)** 同期ルールの編集可能なコピーを削除します 
7. **[Out to AAD – Group Join]\(AAD への送信 – グループへの参加\)** 同期ルールの既定のコピーを有効にします 
8. 管理者として PowerShell プロンプトを開きます 
9. 同期スケジューラを再度有効にします 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> V2 エンドポイントから V1 エンドポイントに切り替えて戻すと、完全同期が実行された後、Azure AD に対してプロビジョニングされた AD グループと、AD に対してプロビジョニングされた Microsoft 365 統合グループの両方で、メンバー数が 50,000 を超える同期済みグループが削除されます。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問  
 
**新しいエンドポイントがアップグレードと新規インストールの既定値になるのはいつですか?**   
</br>2021 年 2 月に AADConnect の新しいリリースをダウンロード用として公開することを予定しています。 このリリースでは、V2 エンドポイントが既定で使用され、追加の構成なしで 50K を超えるグループの同期が可能になります。 このリリースは、その後、対象サーバーへの自動アップグレード用として公開されます。
 
## <a name="next-steps"></a>次のステップ

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
