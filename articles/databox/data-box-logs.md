---
title: Azure Data Box、Azure Data Box Heavy のイベントの追跡とログ記録 | Microsoft Docs
description: Azure Data Box と Azure Data Box Heavy の注文のさまざまな段階でのイベントの追跡とログ記録の方法を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: ba08cd7fdecda99c04d5bb1007b3e5f61cd1bd5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446766"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box と Azure Data Box Heavy の追跡とイベントのログ記録

Data Box または Data Box Heavy の注文は、注文、設定、データのコピー、返却、Azure へのアップロードと確認、およびデータ消去の段階をたどります。 注文の各段階に対応して、注文へのアクセスの制御、イベントの監査、注文の追跡、生成されるさまざまなログの解釈のための、さまざまなアクションを実行できます。

次の表で、Data Box または Data Box Heavy の注文の段階と、各段階で注文の追跡と監査に使用できるツールの概要を示します。

| Data Box 注文の段階       | 追跡と監査のためのツール                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 注文の作成               | [RBAC を使用して注文へのアクセス制御を設定する](#set-up-access-control-on-the-order)                                                    |
| 処理された注文            | 以下を通じて[注文を追跡する](#track-the-order) <ul><li> Azure ポータル </li><li> 運送業者の Web サイト </li><li>電子メール通知</ul> |
| デバイスを設定する              | [アクティビティ ログ](#query-activity-logs-during-setup)に記録されたデバイスの資格情報へのアクセス                                              |
| デバイスへのデータのコピー        | データのコピー用の [*error.xml* ファイルを確認する](#view-error-log-during-data-copy)                                                             |
| 発送の準備をする            | デバイス上のマニフェスト ファイルまたは [BOM ファイルを検査する](#inspect-bom-during-prepare-to-ship)                                      |
| Azure へのデータのアップロード       | Azure データセンターでのデータのアップロード中のエラーについて [*copylogs* を確認する](#review-copy-log-during-upload-to-azure)                         |
| デバイスからのデータの消去   | 監査ログと注文履歴を含む[生産物流管理ログを表示する](#get-chain-of-custody-logs-after-data-erasure)                |

この記事では、Data Box または Data Box Heavy の注文の追跡と監査に使用できるさまざまなメカニズムやツールの詳細について説明します。 この記事の情報は、Data Box と Data Box Heavy の両方に適用されます。 後続のセクションにおいて、Data Box への言及はすべて、Data Box Heavy にも適用されます。

## <a name="set-up-access-control-on-the-order"></a>注文へのアクセス制御を設定する

注文が初めて作成されたときに注文にアクセスできるユーザーを制御できます。 さまざまな範囲のロールベースのアクセス制御 (RBAC) ロールを設定して、Data Box の注文へのアクセスを制御します。 RBAC ロールは、アクセスの種類 (読み取り/書き込み、読み取り専用、操作のサブセットへの読み取り/書き込み) を決定します。

Azure Data Box サービスに対して定義できる 2 つのロールは次のとおりです。

- **Data Box 閲覧者** - スコープによって定義されている、注文に対する読み取り専用アクセス権を持ちます。 表示できるのは注文の詳細のみです。 ストレージ アカウントに関連するその他の詳細にアクセスしたり、住所などの注文の詳細を編集したりすることはできません。
- **Data Box 共同作成者** - *既にストレージ アカウントへの書き込みアクセス権を持っている場合は*、特定のストレージ アカウントにデータを転送するためにのみ注文を作成できます。 ストレージ アカウントへのアクセス権がない場合は、アカウントにデータをコピーするために Data Box の注文を作成することもできません。 このロールは、ストレージ アカウントに関連するアクセス許可を定義することも、ストレージ アカウントにアクセス権を付与することもありません。  

注文へのアクセスを制限するためには、次のようにすることができます。

- 注文レベルでロールを割り当てます。 その特定の Data Box の注文のみを操作し、その他は何も操作しないように、ユーザーはロールによって定義されているアクセス許可のみを持ちます。
- リソース グループ レベルのロールを割り当てます。ユーザーはリソース グループ内のすべての Data Box の注文へのアクセス権を持ちます。

推奨される RBAC の使用の詳細については、[RBAC のベスト プラクティス](../role-based-access-control/overview.md#best-practice-for-using-rbac)に関するページを参照してください。

## <a name="track-the-order"></a>注文を追跡する

運送業者の Web サイトと Azure portal を通じて注文を追跡することができます。 Data Box の注文をいつでも追跡できるよう、次のメカニズムが用意されています。

- デバイスが Azure データセンターまたはオンプレミスにあるときに注文を追跡するには、Azure portal で **[Data Box order]\(Data Box の注文) > [概要]** の順に移動します。

    ![注文の状態と追跡番号の確認](media/data-box-logs/overview-view-status-1.png)

- デバイスの輸送中に注文を追跡するには、地域の運送業者の Web サイト (米国の UPS Web サイトなど) にアクセスします。 注文に関連付けられている追跡番号を入力します。
- また、Data Box は、注文が作成されたときに入力されたメールに基づいて、注文状態が変更されるたびにメール通知も送信します。 Data Box の注文のすべての状態の一覧については、「[注文の状態の確認](data-box-portal-admin.md#view-order-status)を参照してください。 注文に関連付けられている通知の設定を変更する場合は、「[通知の詳細の編集](data-box-portal-admin.md#edit-notification-details)」を参照してください。

## <a name="query-activity-logs-during-setup"></a>設定中のアクティビティ ログのクエリ

- Data Box は、お客様のオンプレミスにロックされた状態で届きます。 ご注文には Azure portal で使用可能なデバイスの資格情報を使用できます。  

    Data Box が設定されている場合は、デバイスの資格情報にアクセスしたすべてのユーザーがわかっている必要がある場合があります。 **[デバイスの資格情報]** ブレードにアクセスしたユーザーを調べるには、アクティビティ ログに対してクエリを実行することができます。   **[デバイスの詳細] > [資格情報]** ブレードへのアクセスに関連するアクションは、`ListCredentials` アクションとしてとしてアクティビティ ログに記録されます。

    ![アクティビティ ログのクエリ](media/data-box-logs/query-activity-log-1.png)

- Data Box へのサインインは、毎回リアルタイムでログに記録されます。 ただし、この情報は、注文が正常に完了した後で、[監査ログ](#audit-logs)でのみ入手できます。

## <a name="view-error-log-during-data-copy"></a>データのコピー中にエラー ログを表示する

Data Box または Azure Data Box Heavy へのデータのコピー中に、コピーしているデータに関する問題が発生した場合は、エラー ファイルが生成されます。

### <a name="errorxml-file"></a>Error.xml ファイル

コピー ジョブがエラーなしで完了したことを確認してください。 コピー プロセス中にエラーがある場合は、 **[接続とコピー]**   ページからログをダウンロードします。

- 512 バイトにアラインされていないファイルを Data Box のマネージド ディスク フォルダーにコピーした場合、そのファイルはステージング用のストレージ アカウントにページ BLOB としてアップロードされません。 ログにエラーが表示されます。 そのファイルを削除し、512 バイトにアラインされているファイルをコピーします。
- VHDX、動的 VHD、または差分 VHD (これらのファイルはサポートされていません) をコピーした場合は、ログにエラーが表示されます。

マネージド ディスクにコピーする際のさまざまなエラーの *error.xml* のサンプルは、次のとおりです。

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

ページ BLOB にコピーする際のさまざまなエラーの *error.xml* のサンプルは、次のとおりです。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


ブロック BLOB にコピーする際のさまざまなエラーの *error.xml* のサンプルは、次のとおりです。

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Azure Files にコピーする際のさまざまなエラーの *error.xml* のサンプルは、次のとおりです。

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

上記のそれぞれのケースで、次の手順に進む前にエラーを解決してください。 SMB または NFS プロトコルを使用した Data Box へのデータのコピー中に受け取ったエラーの詳細については、[Data Box および Data Box Heavy の問題のトラブルシューティング](data-box-troubleshoot.md)に関するページを参照してください。 REST を使用した Data Box へのデータのコピー中に受信したエラーの詳細については、「[Data Box BLOB ストレージに関連する問題のトラブルシューティング](data-box-troubleshoot-rest.md)」を参照してください。

## <a name="inspect-bom-during-prepare-to-ship"></a>発送準備中に BOM を検査する

発送準備中には、部品表 (BOM) と呼ばれるファイルまたはマニフェスト ファイルの一覧が作成されます。

- このファイルを使用して、Data Box にコピーされたファイルの実際の名前と数を確認します。
- このファイルを使用して、ファイルの実際のサイズを確認します。
- *crc64* が 0 以外の文字列に対応することを確認します。 <!--A null value for crc64 indicates that there was a reparse point error)-->

発送準備中に受信したエラーの詳細については、[Data Box および Data Box Heavy の問題のトラブルシューティング](data-box-troubleshoot.md)に関するページを参照してください。

### <a name="bom-or-manifest-file"></a>BOM またはマニフェスト ファイル

BOM または マニフェスト ファイルには、Data Box デバイスにコピーされたすべてのファイルの一覧が含まれています。 BOM ファイルには、ファイル名と対応するサイズ、およびチェックサムが含まれています。 ブロック BLOB、ページ BLOB、Azure Files 用、REST API を使用したコピー用、および Data Box 上のマネージド ディスクへのコピー用に、別個の BOM ファイルが作成されます。 BOM ファイルは、発送準備中にデバイスのローカル Web UI からダウンロードできます。

これらのファイルは Data Box デバイス上にも存在していて、Azure データセンター内の関連付けられているストレージ アカウントにアップロードされます。

### <a name="bom-file-format"></a>BOM ファイルの形式

BOM またはマニフェスト ファイルの一般的な形式は次のとおりです。

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

データが Data Box 上のブロック BLOB の共有にコピーされたときに生成されるマニフェストのサンプルは、次のとおりです。

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

BOM またはマニフェスト ファイルは Azure ストレージ アカウントにもコピーされます。 BOM またはマニフェスト ファイルを使用して、Azure にアップロードされたファイルが Data Box にコピーされたデータに一致することを確認できます。

## <a name="review-copy-log-during-upload-to-azure"></a>Azure へのアップロード中にコピー ログを確認する

Azure へのデータのアップロード中に、*copylog* が作成されます。

### <a name="copylog"></a>Copylog

Data Box サービスでは、処理される注文ごとに、関連付けられたストレージ アカウント内に *copylog* が作成されます。 *copylog* には、アップロードされたファイルの総数と、Data Box から Azure Storage アカウントへのデータのコピー中にエラーが出力されたファイルの数が含まれています。

巡回冗長検査 (CRC) の計算は、Azure へのアップロード中に行われます。 データのコピーからの CRC とデータのアップロード後の CRC が比較されます。 CRC の不一致は、対応するファイルがアップロードに失敗したことを示します。

既定では、ログは  `copylog` という名前のコンテナーに書き込まれます。 ログは、次の命名規則を使用して保存されます。

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`

copylog パスは、ポータルの **[概要]** ブレードにも表示されます。

![完了時に [概要] ブレードに表示されている copylog へのパス](media/data-box-logs/copy-log-path-1.png)

次のサンプルは、正常に完了した Data Box のアップロード用の copylog ファイルの一般的な形式を示しています。

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Azure へのアップロードも、エラーで完了する場合があります。

![エラーで完了したときに [概要] ブレードに表示されている copylog へのパス](media/data-box-logs/copy-log-path-2.png)

次に、アップロードがエラーで完了した場合の copylog の例を示します。

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
次に、Azure の命名規則に準拠していないコンテナーが、Azure へのデータ アップロード中に名前変更された場合の `copylog` の例を示します。

コンテナーの新しい固有名は `DataBox-GUID` という形式で、コンテナーのデータは、名前が変更された新しいコンテナーに配置されます。 この `copylog` では、コンテナーの古い名前と新しい名前が指定されています。

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

次に、Azure の命名規則に準拠していない BLOB またはファイルが、Azure へのデータ アップロード中に名前変更された場合の `copylog` の例を示します。 新しい BLOB 名またはファイル名は、コンテナーへの相対パスの SHA256 ダイジェストに変換され、宛先の種類に基づいてパスにアップロードされます。 宛先として、ブロック BLOB、ページ BLOB、または Azure Files を指定できます。

この `copylog` では、BLOB またはファイルの古い名前と新しい名前、および Azure でのパスが指定されています。

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>データの消去後に生産物流管理ログを取得する

NIST SP 800-88 リビジョン 1 のガイドラインに従って Data Box ディスクからデータが消去された後、生産物流管理ログが使用可能になります。 これらのログには、監査ログと注文履歴が含まれます。 BOM またはマニフェスト ファイルの監査ログと一緒にコピーされます。

### <a name="audit-logs"></a>監査ログ

監査ログには、Azure データセンターの外部にあるときの Data Box または Data Box Heavy の電源投入と共有へのアクセスに関する情報が含まれます。 これらのログは、`storage-account/azuredatabox-chainofcustodylogs` にあります

Data Box からの監査ログのサンプルを次に示します。

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>注文履歴のダウンロード

注文履歴は Azure portal で利用できます。 注文が完了し、デバイスのクリーンアップ (ディスクからのデータ消去) が完了したら、デバイスの注文に移動し、次に **[注文の詳細]** に移動します。 **[注文履歴のダウンロード]**   オプションが使用可能になっています。 詳細については、「[注文履歴のダウンロード](data-box-portal-admin.md#download-order-history)」を参照してください。

注文履歴をスクロールすると、以下の内容が表示されます。

- デバイスの運送業者の追跡情報。
- *SecureErase* アクティビティのイベント。 これらのイベントは、ディスク上のデータの消去に対応します。
- Data Box ログのリンク。 *監査ログ*、*copylog*、および *BOM* ファイルのパスが表示されます。

次に、Azure portal からの注文履歴ログのサンプルを示します。

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>次の手順

- [Data Box および Data Box Heavy の問題のトラブルシューティング](data-box-troubleshoot.md)方法を確認します。
