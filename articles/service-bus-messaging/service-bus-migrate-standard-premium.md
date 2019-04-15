---
title: 既存の Azure Service Bus Standard 名前空間を Premium レベルに移行する | Microsoft Docs
description: 既存の Azure Service Bus Standard 名前空間を Premium に移行するためのガイドです
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896724"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>既存の Azure Service Bus Standard 名前空間を Premium レベルに移行する

以前は、Azure Service Bus では Standard レベルでのみ名前空間が提供されていました。 これらは、低スループット環境と開発者環境用に最適化されたマルチテナントの設定でした。

最近、Azure Service Bus は Premium レベルを提供するように拡張されました。このレベルは、予測可能な待機時間と高いスループットのために名前空間ごとに専用のリソースを固定価格で提供し、追加のエンタープライズ機能を必要とする高スループット環境および運用環境用に最適化されています。

以下のツールにより、既存の Standard レベルの名前空間を Premium レベルに移行できます。

>[!WARNING]
> 移行は、Service Bus の Standard 名前空間を Premium レベルに "***アップグレード***" するためのものです。
>
> 移行ツールでは、ダウングレードはサポートされて "***いません***"。
>[!NOTE]
> この移行は、"***インプレース***" で行うことが意図されています。
>
> つまり、既存の送信側と受信側のアプリケーションでコードまたは構成を変更する必要はありません。
>
> 既存の接続文字列は、新しい Premium 名前空間を自動的に指すようになります。
>
> さらに、Standard 名前空間のすべてのエンティティは、移行プロセスの間に Premium 名前空間に**コピー**されます。
>
>
> Premium では***メッセージング ユニットあたり 1,000 エンティティ***がサポートされるので、必要なメッセージング ユニットの数を明らかにするには、現在の Standard 名前空間に存在するエンティティの数から始めてください。

## <a name="migration-steps"></a>移行の手順

>[!IMPORTANT]
> 移行プロセスに関してはいくつかの注意事項があります。 エラーの可能性を減らすため、関係する手順を完全に理解してください。

以下のガイドでは、具体的な移行プロセスの手順を詳しく説明します。

論理的な手順は次のとおりです。

1. 新しい Premium 名前空間を作成します。
2. Standard 名前空間と Premium 名前空間を相互にペアにします。
3. Standard 名前空間から Premium 名前空間にエンティティを同期 (コピー) します
4. 移行をコミットします
5. 名前空間の移行後の名前を使用して、Standard 名前空間内のエンティティをドレインします
6. Standard 名前空間を削除します

>[!NOTE]
> 移行をコミットした後、古い Standard 名前空間にアクセスして、キューとサブスクリプションをドレインすることが非常に重要です。
>
> メッセージをドレインすると、メッセージを新しい Premium 名前空間に送信し、受信側アプリケーションで処理できるようになります。
>
> キューとサブスクリプションをドレインした後は、古い Standard 名前空間を削除することをお勧めします。 もう必要ありません。

### <a name="migrate-using-azure-cli-or-powershell"></a>Azure CLI または PowerShell を使用して移行する

Azure CLI または PowerShell ツールを使用して Service Bus の Standard 名前空間を Premium に移行するには、以下のガイドを参照してください。

1. 新しい Service Bus Premium 名前空間を作成します。 [Azure Resource Manager テンプレート](service-bus-resource-manager-namespace.md)に関する記事、または [Azure portal の使用](service-bus-create-namespace-portal.md)に関する記事をご覧ください。 **serviceBusSku** パラメーターに対して必ず "Premium" を選択してください。

2. 以下の環境変数を設定して、移行コマンドを簡略化します。
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > 移行後に古い Standard 名前空間にアクセスするには、移行後の名前 (post_migration_dns_name) を使用します。 キューとサブスクリプションをドレインした後、名前空間を削除するには、これを使用する必要があります。

3. Standard 名前空間と Premium 名前空間を**ペア**にし、次のコマンドを使用して**同期を開始**します。

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. 次のコマンドを使用して、移行の状態を確認します。
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    以下であれば、移行は完了したものとみなされます
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    このコマンドでは、移行の構成も表示されます。 値が以前の宣言のように設定されていることを再確認してください。

    さらに、ポータルで Premium 名前空間を調べて、すべてのキューとトピックが作成されていること、およびそれらが Standard 名前空間に存在していたものと一致することを確認します。

5. 次の Complete コマンドを実行して、移行をコミットします
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>Azure portal を使用して移行する

Azure portal を使用した移行の論理フローは、コマンドを使用した移行と同じです。 ポータルを使用した移行の詳細な手順は、以下のガイドを参照してください。

1. 左側のウィンドウのナビゲーション メニューで、**[Premium に移行]** メニュー オプションを選択します。 **[作業を開始する]** ボタンをクリックして、次のページに進みます。
    ![移行のランディング ページ][]

2. **[セットアップ]** を完了します。
   ![名前空間をセットアップする][]
   1. 既存の Standard 名前空間を移行する Premium 名前空間を作成して割り当てます。
        ![名前空間のセットアップ - Premium 名前空間を作成する][]
   2. 移行が完了した後で Standard 名前空間にアクセスするための **[移行後の名前]** を選択します。
        ![名前空間のセットアップ - 移行後の名前を選択する][]
   3. **[次へ]** をクリックして続行します。
3. Standard 名前空間と Premium 名前空間の間でエンティティを**同期**します。
    ![名前空間のセットアップ - エンティティを同期する - 開始][]

   1. **[同期の開始]** をクリックして、エンティティの同期を開始します。
   2. 表示されるポップアップで **[はい]** クリックして、同期の開始を確認します。
   3. **同期**が完了するまで待ちます。 ステータス バーで状態を見ることができます。
        ![名前空間のセットアップ - エンティティを同期する - 進行][]
        >[!IMPORTANT]
        > 何らかの理由で**中止**する必要がある場合は、このドキュメントの FAQ セクションにある中止のフローを確認してください。
   4. 同期が完了したら、ページの下部にある **[次へ]** ボタンをクリックします。

4. 概要ページで変更を確認します。
    ![名前空間を切り替える - スイッチ メニュー][]

5. **[移行の完了]** をクリックして名前空間を切り替え、移行を完了します。
    ![名前空間を切り替える - 成功][]

## <a name="faqs"></a>FAQ

### <a name="what-happens-when-the-migration-is-committed"></a>移行をコミットするとどうなりますか?

移行をコミットした後、Standard 名前空間を指していた接続文字列は、Premium 名前空間を指すようになります。

送信側と受信側のアプリケーションは Standard 名前空間から切断され、Premium 名前空間に自動的に再接続されます。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Standard から Premium への移行が完了した後は、どうすればよいですか?

Standard から Premium への移行では、エンティティ メタデータ (トピック、サブスクリプション、フィルターなど) が Standard 名前空間から Premium 名前空間にコピーされることが保証されています。 Standard 名前空間にコミットされたメッセージ データは、Standard 名前空間から Premium 名前空間にコピーされません。

このため、Standard 名前空間には、移行の進行中に送信されてコミットされたメッセージが存在する可能性があります。 これらのメッセージは、手動で Standard 名前空間からドレインし、Premium 名前空間に手動で送信する必要があります。

これを行うには、移行コマンドで指定した**移行後の DNS 名**を使用して Standard 名前空間のエンティティをドレインするコンソール アプリまたはスクリプトを使用した後、これらのメッセージを Premium 名前空間に送信して、受信側で処理できるようにする "***必要があります***"。

メッセージをドレインした後は、Standard 名前空間の削除に進んでください。

>[!IMPORTANT]
> Standard 名前空間からメッセージをドレインした後は、Standard 名前空間を削除する "**必要がある**" ことに注意してください。
>
> 最初は Standard 名前空間を参照していた接続文字列が、実際は Premium 名前空間を参照するようになっているため、これは重要なことです。 この Standard 名前空間が必要になることはありません。
>
> 移行した Standard 名前空間を削除しておくと、後日の混乱を減らすのに役立ちます。 

### <a name="how-much-downtime-do-i-expect"></a>どの程度のダウンタイムが想定されますか?
上で説明した移行プロセスは、アプリケーションに予想されるダウンタイムを短縮するためのものです。 これは、送信側と受信側のアプリケーションが新しい Premium 名前空間を指すために使用する接続文字列を利用することによって行われます。

アプリケーションで発生するダウンタイムは、Premium 名前空間を指すように DNS エントリを更新するのにかかる時間に限定されます。

これは、***約 5 分間***と想定できます。

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>移行を実行する間に構成を変更する必要はありますか?
いいえ、この移行を実行するために、コード/構成の変更は必要ありません。 送信側と受信側のアプリケーションが Standard 名前空間にアクセスするために使用する接続文字列は、Premium 名前空間に対する**別名**として機能するように自動的にマップされます。

### <a name="what-happens-when-i-abort-the-migration"></a>移行を中止するとどうなりますか?
移行は、"Abort" コマンドを使用して、または Azure portal から、中止することができます。 

#### <a name="azure-cli-or-powershell"></a>Azure CLI または PowerShell

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Azure ポータル

![フローを中止する - 同期を中止][]
![フローを中止する - 中止完了][]

移行プロセスを中止すると、実際には、Standard 名前空間から Premium 名前空間へのエンティティ (トピック、サブスクリプション、フィルター) のコピー プロセスが中止され、ペアリングが解除されます。

接続文字列は、Premium 名前空間を指すように更新**されません**。 既存のアプリケーションは、移行を始める前と同様に機能し続けます。

ただし、Premium 名前空間のエンティティまたは Premium 名前空間自体が削除されることは**ありません**。 移行をまったく行わないようになった場合は、これを手動で行う必要があります。

>[!IMPORTANT]
> 移行を中止する場合は、移行用にプロビジョニングした Premium 名前空間を削除し、リソースに課金されないようにしてください。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>メッセージのドレインを必要にしたくありません。 どうすればよいですか。

移行が行われている間の、移行がコミットされる直前に、送信側アプリケーションによって送信されて、Standard 名前空間のストレージにコミットされたメッセージが存在する可能性があります。

移行の間に実際のメッセージ データ/ペイロードが Standard から Premium にコピーされない場合、これらを手動でドレインし、Premium 名前空間に送信する必要があります。

ただし、計画的なメンテナンス/ハウスキープ処理期間中に移行でき、メッセージを手動でドレインして送信したくない場合は、以下の手順のようにしてください。

1. 送信側アプリケーションを停止し、受信側が現在 Standard 名前空間にあるメッセージを処理してキューをドレインできるようにします。
2. Standard 名前空間のキューとサブスクリプションが空になったら、上で説明した手順に従い、Standard 名前空間から Premium 名前空間への移行を実行します。
3. 移行が完了したら、送信側アプリケーションを再起動できます。
4. 送信側と受信側は、Premium 名前空間と自動的に接続します。

    >[!NOTE]
    > 移行のために受信側を停止する必要はありません。
    >
    > 移行が完了すると、受信側は Standard 名前空間から切断され、Premium 名前空間に自動的に接続されます。

## <a name="next-steps"></a>次の手順

* [Standard と Premium のメッセージングの違い](./service-bus-premium-messaging.md)についてさらに詳しく学習してください
* Service Bus Premium の高可用性と geo ディザスター リカバリーの側面については、[こちら](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)をご覧ください

[移行のランディング ページ]: ./media/service-bus-standard-premium-migration/1.png
[名前空間をセットアップする]: ./media/service-bus-standard-premium-migration/2.png
[名前空間のセットアップ - Premium 名前空間を作成する]: ./media/service-bus-standard-premium-migration/3.png
[名前空間のセットアップ - 移行後の名前を選択する]: ./media/service-bus-standard-premium-migration/4.png
[名前空間のセットアップ - エンティティを同期する - 開始]: ./media/service-bus-standard-premium-migration/5.png
[名前空間のセットアップ - エンティティを同期する - 進行]: ./media/service-bus-standard-premium-migration/8.png
[名前空間を切り替える - スイッチ メニュー]: ./media/service-bus-standard-premium-migration/9.png
[名前空間を切り替える - 成功]: ./media/service-bus-standard-premium-migration/12.png

[フローを中止する - 同期を中止]: ./media/service-bus-standard-premium-migration/abort1.png
[フローを中止する - 中止完了]: ./media/service-bus-standard-premium-migration/abort3.png
