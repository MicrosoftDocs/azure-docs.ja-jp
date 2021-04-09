---
title: Azure AD Connect:旧バージョンからアップグレードする | Microsoft Docs
description: インプレース アップグレードとスウィング移行移行など、Azure Active Directory Connect の最新リリースにアップグレードするさまざまな方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36b7fce2e2ccb6f331e42e8052ef4fb75d35e831
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729992"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect:旧バージョンから最新バージョンにアップグレードする
このトピックでは、Azure Active Directory (Azure AD) Connect のインストールを最新リリースにアップグレードするさまざまな方法について説明します。  構成を大幅に変更する際は、「[スウィング移行](#swing-migration)」で説明されている手順を使用することもできます。

>[!NOTE]
> Azure AD Connect の最新リリースでサーバーを最新の状態に保つことが重要です。 AADConnect に対するアップグレードは絶えず行われており、これらのアップグレードには、セキュリティの問題およびバグの修正プログラムの他、サービス性、パフォーマンス、スケーラビリティの向上が含まれます。 最新バージョンを確認し、バージョン間で行われた変更点を把握するには、[リリース バージョン履歴](./reference-connect-version-history.md)に関する記事を参照してください

>[!NOTE]
> 現在、Azure AD Connect は、任意のバージョンから最新バージョンへのアップグレードがサポートされています。 DirSync または ADSync のインプレース アップグレードはサポートされておらず、スウィング移行が必要となります。  DirSync からアップグレードする場合は、[Azure AD 同期ツール (DirSync) からのアップグレード](how-to-dirsync-upgrade-get-started.md)に関するページまたは「[Swing migration (スウィング移行)](#swing-migration)」セクションを参照してください。  </br>実際には、極端に古いバージョンをご使用の場合、Azure AD Connect には直接関係のない問題が発生する可能性はあります。 何年にもわたって運用されてきたサーバーは通常、さまざまなパッチが適用されており、その一部が考慮されていないことも考えられます。  一般に、12 か月から 18 か月間アップグレードを行っていないお客様は、スウィング アップグレードを検討してください。スウィング アップグレードが最も慎重でリスクの少ない選択肢です。

DirSync からアップグレードする場合は、代わりに [Azure AD 同期ツール (DirSync) からのアップグレード](how-to-dirsync-upgrade-get-started.md)を参照してください。

Azure AD Connect のアップグレードで使用できる方法は複数あります。

| Method | 説明 |
| --- | --- |
| [自動アップグレード](how-to-connect-install-automatic-upgrade.md) |ユーザーにとって、高速インストールは最も簡単な方法です。 |
| [インプレース アップグレード](#in-place-upgrade) |サーバーが 1 台だけの場合は、同じサーバーでインストールをインプレース アップグレードできます。 |
| [スウィング移行](#swing-migration) |2 台のサーバーを用意し、一方に新しいリリースまたは構成を用意して、準備ができたらアクティブなサーバーを変更します。 |

アクセス許可に関する情報については、[アップグレードに必要なアクセス許可](reference-connect-accounts-permissions.md#upgrade)に関するセクションをご覧ください。

> [!NOTE]
> 新しい Azure AD Connect サーバーが Azure AD に対する変更の同期を開始できるようにした後は、DirSync または Azure AD Sync を使用してロールバックしないでください。Azure AD Connect から DirSync、Azure AD Sync などの従来のクライアントへのダウングレードはサポートされておらず、Azure AD のデータ損失などの問題につながる場合があります。

## <a name="in-place-upgrade"></a>一括アップグレード
インプレース アップグレードは、Azure AD Sync または Azure AD Connect からの移動に使用できます。 DirSync からの移動、または Forefront Identity Manager (FIM) + Azure AD コネクタのソリューションには使用できません。

この方法は、サーバーが 1 台でオブジェクトが約 100,000 未満の場合にお勧めします。 標準の同期規則に対する変更があった場合は、アップグレード後にフル インポートと完全同期が実行されます。 この方法により、新しい構成がシステムのすべての既存のオブジェクトに適用されることが保証されます。 同期エンジンのスコープ内のオブジェクトの数によっては、数時間かかることがあります。 通常の差分同期スケジューラー (既定では 30 分ごとに同期) は中断されますが、パスワード同期は継続されます。 インプレース アップグレードは週末に実行するよう検討してください。 新しい Azure AD Connect リリースで標準構成に変更がなかった場合は、通常の差分インポートまたは差分同期が開始します。  
![一括アップグレード](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

標準の同期規則を変更した場合は、これらの規則はアップグレード時に既定の構成に戻ります。 アップグレードの前後で構成が維持されていることを確認するには、[既定の構成を変更するためのベスト プラクティス](how-to-connect-sync-best-practices-changing-default-configuration.md)に関するページの説明に従って変更を行ってください。

インプレース アップグレード中、アップグレード後に特定の同期アクティビティ (フル インポート手順、完全同期手順など) の実行を必要とする変更が行われる可能性があります。 このようなアクティビティを保留にするには、「[アップグレード後に完全な同期を保留にする方法](#how-to-defer-full-synchronization-after-upgrade)」を参照してください。

非標準のコネクタ (Generic LDAP コネクタや Generic SQL コネクタなど) で Azure AD Connect を使用している場合は、インプレース アップグレード後に [Synchronization Service Manager](./how-to-connect-sync-service-manager-ui-connectors.md) で対応するコネクタ構成を更新する必要があります。 コネクタ構成を更新する方法の詳細については、「[コネクタ バージョンのリリース履歴 - トラブルシューティング](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#troubleshooting)」をご覧ください。 構成を更新していない場合、インポート/エクスポートの実行手順がコネクタで正しく動作しなくなります。 アプリケーション イベント ログに、" *&quot;Assembly version in AAD Connector configuration (&quot;X.X.XXX.X") is earlier than the actual version ("X.X.XXX.X") of "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll""\(AAD コネクタ構成 ("X.X.XXX.X") のアセンブリ バージョンが、"C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll" の実際のバージョン ("X.X.XXX.X") より前のバージョンです\)* " というエラー メッセージが表示されます。

## <a name="swing-migration"></a>スウィング移行
複雑なデプロイまたは多くのオブジェクトがある場合は、ライブ システムでのインプレース アップグレードが現実的ではない場合があります。 ユーザーによっては、このプロセスに数日かかることがあり、この間差分変更は処理されません。 構成を大幅に変更する予定があり、テストを行ってからクラウドにプッシュしたい場合にもこの方法は使用できます。

このようなシナリオでは、スウィング移行を使用することをお勧めします。 アクティブ サーバーが 1 台とステージング サーバーが 1 台、(少なくとも) 2 台のサーバーが必要です。 アクティブ サーバー (次の図の青い実線) では、アクティブな運用負荷を処理します。 ステージング サーバー (次の図の紫の破線) では新しいリリースまたは構成を準備します。 このサーバーの準備ができたら、アクティブになります。 古くなったバージョンまたは構成がインストールされている前のアクティブ サーバーは、ステージング サーバーになりアップグレードされます。

2 つのサーバーには、それぞれ異なるバージョンを使用できます。 たとえば、使用を停止する予定のアクティブ サーバーでは Azure AD Sync を使用でき、新しいステージング サーバーでは Azure AD Connect を使用できます。 スウィング移行を使用して新しい構成を開発する場合は、2 つのサーバーで同じバージョンを使用することをお勧めします。  
![ステージング サーバー](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> このシナリオでは、3 台または 4 台のサーバーを使用することが好まれる場合があります。 ステージング サーバーがアップグレードされているときに、[障害復旧](how-to-connect-sync-staging-server.md#disaster-recovery)用のバックアップ サーバーがないためです。 3 台か 4 台のサーバーを使用すると、新しいバージョンのプライマリ/スタンバイ サーバーのセットを用意でき、引き継ぎ用のステージング サーバーが常に確保できます。

以下の手順は、Azure AD Sync または FIM + Azure AD Connector のソリューションからの移行にも使用できます。 この手順は DirSync には使用できませんが、DirSync 用の手順が組み込まれた同じスウィング移行方法 (並列デプロイとも呼ばれます) が、[Azure Active Directory 同期 (DirSync) のアップグレード](how-to-dirsync-upgrade-get-started.md)に関するページで説明されています。

### <a name="use-a-swing-migration-to-upgrade"></a>スウィング移行を使用してアップグレードする
1. 両方のサーバーで Azure AD Connect を使用し、構成の変更だけを行う予定の場合は、アクティブ サーバーとステージング サーバーで同じバージョンを使用していることを確認します。 同じバージョンを使用することで、後で違いを比較しやすくなります。 Azure AD Sync からアップグレードする場合、2 つのサーバーのバージョンは異なるものになります。 Azure AD Connect の前のバージョンからアップグレードする場合は、2 つのサーバーで同じバージョンを使用している状態から開始することをお勧めしますが、必須ではありません。
2. カスタム構成を行っていて、ステージング サーバーにそれが含まれていない場合は、「[カスタム構成のアクティブ サーバーからステージング サーバーへの移動](#move-a-custom-configuration-from-the-active-server-to-the-staging-server)」の手順に従ってください。
3. Azure AD Connect の以前のリリースからアップグレードする場合は、ステージング サーバーを最新のバージョンにアップグレードします。 Azure AD Sync から移行する場合は、ステージング サーバーに Azure AD Connect をインストールします。
4. 同期エンジンがステージング サーバーで完全なインポートと完全な同期を実行するまで待ちます。
5. 「[サーバーの構成の確認](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server)」の「確認」の手順を使用して、新しい構成で予期しない変更が発生していないことを確認します。 予期しないことがあった場合は、次の手順で問題がなくなるまで、修正、インポートと同期の実行、データの確認を繰り返します。
6. ステージング サーバーをアクティブ サーバーに切り替えます。 これは、「[サーバーの構成の確認](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server)」の最後の手順である「アクティブなサーバーの切り替え」にあたります。
7. Azure AD Connect をアップグレードする場合は、ステージング モードになっているサーバーを最新リリースにアップグレードします。 前と同じ手順に従って、データと構成をアップグレードします。 Azure AD Sync からアップグレードしている場合は、ここで、以前のサーバーの電源を切って、使用を停止できます。

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>カスタム構成のアクティブ サーバーからステージング サーバーへの移動
アクティブ サーバーの構成を変更してある場合は、ステージング サーバーに同じ変更が適用されていることを確認する必要があります。 この方法については、[Azure AD Connect 構成の解析ツール](https://github.com/Microsoft/AADConnectConfigDocumenter)を参照してください。

作成したカスタム同期規則は、PowerShell で移動できます。 その他の変更は両方のシステムで同じように適用する必要があり、変更は移行できません。 [構成の解析ツール](https://github.com/Microsoft/AADConnectConfigDocumenter)を使用すると、2 つのシステムを比較して両者が同一であることを確認するのに役立ちます。 また、このツールはこのセクションにある手順を自動化するのにも役立ちます。

次の項目を両方のサーバーで同じように構成する必要があります。

* 同じフォレストへの接続
* ドメインと OU のすべてのフィルター処理
* 同じオプション機能 (パスワード同期やパスワード ライトバックなど)

**カスタム同期規則の移動**  
カスタム同期規則を移動するには次のようにします。

1. アクティブ サーバーで **同期規則エディター** を開きます。
2. カスタム規則を選択します。 **[エクスポート]** をクリックします。 メモ帳ウィンドウが表示されます。 一時ファイルを PS1 という拡張子で保存します。 そうすることで、PowerShell スクリプトになります。 PS1 ファイルをステージング サーバーにコピーします。  
   ![同期規則のエクスポート](./media/how-to-upgrade-previous-version/exportrule.png)
3. コネクタの GUID は、ステージング サーバーでは異なるものになり、変更する必要があります。 GUID を取得するには、**同期規則エディター** を起動し、同じ接続先システムを表す既定の規則のいずれかを選択して、 **[エクスポート]** をクリックします。 PS1 ファイルの GUID を、ステージング サーバーから取得した GUID に置き換えます。
4. PowerShell プロンプトで、PS1 ファイルを実行します。 これにより、ステージング サーバーにカスタム同期規則が作成されます。
5. すべてのカスタム規則について、これを繰り返します。

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>アップグレード後に完全な同期を保留にする方法
インプレース アップグレード中、特定の同期アクティビティ (フル インポート手順、完全同期手順など) の実行を必要とする変更が行われる可能性があります。 たとえば、コネクタ スキーマを変更した場合は **フル インポート** 手順を、既定の同期規則を変更した場合は **完全同期** 手順を、影響を受けるコネクタで実行する必要があります。 アップグレード中、Azure AD Connect が、必要な同期アクティビティを判断し、"*オーバーライド*" として記録します。 次の同期サイクルで、同期スケジューラはこうしたオーバーライドを取得して、実行します。 オーバーライドは、正常に実行された時点で削除されます。

こうしたオーバーライドを、アップグレードの直後に実行したくない場合があります。 たとえば、同期されたオブジェクトが多数あり、こうした同期手順を営業時間後に実行したい場合などです。 こうしたオーバーライドを削除するには:

1. アップグレード中、 **[構成が完了したら、同期プロセスを開始してください]** オプションを **オフ** にします。 これにより同期スケジューラが無効になり、オーバーライドが削除される前に、同期サイクルが自動的に実行されることがなくなります。

   ![オフにする必要がある [構成が完了したら、同期プロセスを開始してください] オプションが強調表示されているスクリーンショット。](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. アップグレードの完了後、次のコマンドレットを実行して、追加されたオーバーライドを確認します: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > オーバーライドはコネクタ固有です。 次の例では、フル インポート手順と完全同期手順が、オンプレミスの AD コネクタと Azure AD コネクタの両方に追加されます。

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. 追加された既存のオーバーライドを書き留めてください。
   
4. 任意のコネクタでフル インポートと完全同期の両方に対するオーバーライドを削除するには、次のコマンドレットを実行します: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   すべてのコネクタでオーバーライドを削除するには、次の PowerShell スクリプトを実行します。

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. スケジューラを再開するには、次のコマンドレットを実行します: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > 必要な同期手順は、できるだけ早く実行してください。 Synchronization Service Manager を使用してこの手順を手動で実行するか、Set-ADSyncSchedulerConnectorOverride コマンドレットを使用して、オーバーライドを戻すことができます。

任意のコネクタでフル インポートと完全同期の両方に対するオーバーライドを追加するには、次のコマンドレットを実行します: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>トラブルシューティング
次のセクションには、Azure AD Connect のアップグレード時に問題が発生した場合に使用できるトラブルシューティングと情報が含まれています。

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Azure AD Connect のアップグレード時に Azure Active Directory コネクタが存在しないというエラーが発生する

Azure AD Connect を以前のバージョンからアップグレードするとき、その最初の段階で次のエラーに遭遇することがあります。 

![エラー](./media/how-to-upgrade-previous-version/error1.png)

このエラーは、Azure Active Directory コネクタ (ID b891884f-051e-4a83-95af-2544101c9083) が現在の Azure AD Connect の構成に存在しないことが原因で発生します。 それが事実であるかどうかを確認するには、PowerShell ウィンドウを開いて `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083` コマンドレットを実行します。

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

PowerShell コマンドレットから "**the specified MA could not be found (指定された MA が見つかりませんでした)** " というエラーが報告されます。

これが発生する原因は、現在の Azure AD Connect の構成がアップグレードでサポートされていないためです。 

新しいバージョンの Azure AD Connect をインストールする場合は、Azure AD Connect ウィザードを閉じ、既存の Azure AD Connect をアンインストールして、新しい Azure AD Connect のクリーン インストールを実行してください。



## <a name="next-steps"></a>次のステップ
[オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)に関する記事をご覧ください。