---
title: Azure Data Lake Storage アカウントで複数の HDInsight クラスターを使用する - Azure
description: 1 つの Data Lake Storage アカウントで複数の HDInsight クラスターを使用する方法を学習する
keywords: hdinsight ストレージ,hdfs,構造化データ,非構造化データ, Data Lake Store
services: hdinsight,storage
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 0d57c65c93ffcd6c4c5249a1e5effeb457ed1736
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440898"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Azure Data Lake Storage アカウントで複数の HDInsight クラスターを使用する

HDInsight バージョン 3.5 からは、Azure Data Lake Storage アカウントを既定のファイル システムとして使用して HDInsight クラスターを作成できます。
Data Lake Storage は、無制限のストレージをサポートしているため、大量のデータのホスティングだけでなく、1 つの Data Lake Storage アカウントを共有する複数の HDInsight クラスターのホスティングにも最適です。 Data Lake Storage で HDInsight クラスターを作成する方法の手順については、「[クイック スタート:HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」をご覧ください。

この記事では、複数の**アクティブな** HDInsight クラスターにわたって使用できる 1 つの、および共有された Data Lake Storage アカウントを設定するための Data Lake Storage 管理者への推奨事項を示します。 これらの推奨事項は、共有された Data Lake Storage アカウント上の複数のセキュリティ保護された、およびセキュリティ保護されていない Apache Hadoop クラスターのホスティングに適用されます。


## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage のファイルおよびフォルダー レベルの ACL

この記事の残りの部分では、Azure Data Lake Storage でのファイルおよびフォルダー レベルの ACL の十分な知識があることを前提にしています。これについては、「[Azure Data Lake Storage でのアクセス制御](../data-lake-store/data-lake-store-access-control.md)」で詳細に説明されています。

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>複数の HDInsight クラスターのための Data Lake Storage のセットアップ
Data Lake Storage アカウントで複数の HDInsight クラスターを使用するための推奨事項を説明するために、2 つのレベルのフォルダー階層を取り上げてみましょう。 **/clusters/finance** というフォルダー構造を持つ Data Lake Storage アカウントがあるとします。 この構造では、財務組織に必要なすべてのクラスターが保存先として /clusters/finance を使用できます。 将来、別の組織 (たとえば、マーケティング) が同じ Data Lake Storage アカウントを使用して HDInsight クラスターを作成しようとする場合は、/clusters/marketing を作成できます。 今のところは、**/clusters/finance** だけを使用しましょう。

このフォルダー構造を HDInsight クラスターが効率的に使用できるようにするには、次の表に示すように、Data Lake Storage 管理者が適切なアクセス許可を割り当てる必要があります。 表に示されているアクセス許可は既定の ACL ではなく、アクセス ACL に対応します。 


|フォルダー  |アクセス許可  |所有ユーザー  |所有グループ  | 名前付きユーザー | 名前付きユーザーのアクセス許可 | 名前付きグループ | 名前付きグループのアクセス許可 |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |サービス プリンシパル |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |admin |admin |サービス プリンシパル |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |admin |FINGRP  |サービス プリンシパル |rwx  |-  |-     |

この表で、

- **admin** は、Data Lake Storage アカウントの作成者および管理者です。
- **サービス プリンシパル**は、そのアカウントに関連付けられている Azure Active Directory (AAD) サービス プリンシパルです。
- **FINGRP**は、財務組織のユーザーを含む AAD で作成されたユーザー グループです。

AAD アプリケーション (これはサービス プリンシパルも作成します) を作成する方法については、「[AAD アプリケーションの作成](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)」を参照してください。 AAD でユーザー グループを作成する方法については、「[Azure Active Directory でのグループの管理](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

いくつかの考慮すべき重要な点。

- クラスターに対してストレージ アカウントを使用する**前に**、2 つのレベルのフォルダー構造 (**/clusters/finance/**) が Data Lake Storage 管理者によって適切なアクセス許可で作成およびプロビジョニングされている必要があります。 この構造は、クラスターの作成中に自動的に作成されるわけではありません。
- 上の例では、**/clusters/finance** の所有グループを **FINGRP** として設定し、FINGRP にルートから始まるフォルダー階層全体への **r-x** アクセス権を許可することを推奨しています。 これにより、FINGRP のメンバーはルートから始まるフォルダー構造内を移動できることが保証されます。
- 別の AAD サービス プリンシパルが **/clusters/finance** の下にクラスターを作成できる場合は、スティッキー ビット (**finance** フォルダーに設定されている場合) により、あるサービス プリンシパルによって作成されたフォルダーを他のサービス プリンシパルは削除できないことが保証されます。
- フォルダー構造とアクセス許可が適切に設定されていると、HDInsight クラスターの作成プロセスによってクラスター固有の保存場所が **/clusters/finance/** の下に作成されます。 たとえば、fincluster01 という名前を持つクラスターのストレージは **/clusters/finance/fincluster01** になります。 次の表に、HDInsight クラスターによって作成されたフォルダーの所有権とアクセス許可を示します。

    |フォルダー  |アクセス許可  |所有ユーザー  |所有グループ  | 名前付きユーザー | 名前付きユーザーのアクセス許可 | 名前付きグループ | 名前付きグループのアクセス許可 |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |サービス プリンシパル |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>ジョブ入力および出力データの推奨事項

ジョブへの入力データ、およびジョブからの出力を **/clusters** の外部のフォルダーに格納することをお勧めします。 これにより、記憶域スペースを再利用するためにクラスター固有のフォルダーが削除された場合でも、ジョブ入力および出力を引き続き将来の使用のために確保できることが保証されます。 このような場合は、ジョブ入力および出力を格納するためのフォルダー階層によってサービス プリンシパルの適切なレベルのアクセスが許可されることを確認します。

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>1 つのストレージ アカウントを共有するクラスターに関する制限

1 つの Data Lake Storage アカウントを共有できるクラスターの数に関する制限は、それらのクラスターで実行されているワークロードによって異なります。 クラスター数が多すぎる場合や、ストレージ アカウントを共有するクラスター上のワークロードの負荷が非常に高い場合は、ストレージ アカウントの受信/送信が調整されることがあります。

## <a name="support-for-default-acls"></a>既定の ACL のサポート

名前付きユーザーのアクセス権でサービス プリンシパルを作成する場合は (上の表に示しています)、既定の ACL を持つ名前付きユーザーを追加**しないようにする**ことをお勧めします。 既定の ACL を使用して名前付きユーザーのアクセス権をプロビジョニングすると、所有ユーザー、所有グループ、およびその他に対して 770 のアクセス許可が割り当てられます。 この 770 の既定値は所有ユーザー (7) または所有グループ (7) からアクセス許可を除去しませんが、その他 (0) のすべてのアクセス許可を除去します。 これにより、「[既知の問題と対処法](#known-issues-and-workarounds)」のセクションで詳細に説明されている、1 つの特定のユースケースでの既知の問題が発生します。

## <a name="known-issues-and-workarounds"></a>既知の問題と対処法

このセクションでは、Data Lake Storage で HDInsight を使用するための既知の問題とそれらの対処法を一覧表示します。

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>公開されているローカライズされた Apache Hadoop YARN リソース

新しい Azure Data Lake Storage アカウントが作成されると、ルート ディレクトリは、アクセス ACL のアクセス許可ビットが 770 に設定されて自動的にプロビジョニングされます。 ルート フォルダーの所有ユーザーは、アカウントを作成したユーザー (Data Lake Storage 管理者) に設定され、所有グループは、アカウントを作成したユーザーのプライマリ グループに設定されます。 "その他" にアクセス権は提供されません。

これらの設定は、[YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247) で取得された 1 つの特定の HDInsight ユースケースに影響を与えることがわかっています。 ジョブの送信は、次のようなエラー メッセージで失敗することがあります。

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

以前にリンクされた YARN JIRA に示されているように、パブリック リソースのローカライズ中に、ローカライザーはリモート ファイル システムに対するアクセス許可をチェックすることによって、要求されたすべてのリソースが実際にパブリックであることを検証します。 その条件に適合しない LocalResource はすべて、ローカライズに対して拒否されます。 アクセス許可のチェックには、"その他" のファイルへの読み取りアクセス権が含まれます。 Azure Data Lake はルート フォルダー レベルにある "その他" へのすべてのアクセスを拒否するため、Azure Data Lake 上に HDInsight クラスターをホストしている場合、そのままではこのシナリオは機能しません。

#### <a name="workaround"></a>対処法
階層を通して (たとえば、上の表に示すように **/**、**/clusters**、および **/clusters/finance** にある) **その他**に対して読み取り/実行のアクセス許可を設定します。

## <a name="see-also"></a>関連項目

* [クイック スタート:HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](hdinsight-hadoop-use-data-lake-storage-gen2.md)
