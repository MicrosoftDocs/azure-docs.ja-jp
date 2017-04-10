## <a name="fileshare-dataset-type-properties"></a>FileShare データセットの type プロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](../articles/data-factory/data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型でほぼ同じです。

**typeProperties** セクションは、データセットの型ごとに異なります。 これは、データセット型に固有の情報を提供します。 **FileShare** 型のデータセットの typeProperties セクションには、次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |フォルダーへのサブ パス。 文字列内の特殊文字にはエスケープ文字 "\" を使用します。 例については、「 [サンプルのリンクされたサービスとデータセットの定義](#sample-linked-service-and-dataset-definitions) 」ご覧ください。<br/><br/>このプロパティを **partitionBy** と組み合わせて、スライスの開始/終了日時に基づくフォルダー パスを使用できます。 |はい |
| fileName |テーブルでフォルダー内の特定のファイルを参照するには、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、テーブルはフォルダー内のすべてのファイルを参照します。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイルの名前は次の形式になります。 <br/><br/>Data.<Guid>.txt (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |いいえ |
| fileFilter |すべてのファイルではなく、folderPath 内のファイルのサブセットを選択するために使用するフィルターを指定します。<br/><br/>使用可能な値: `*` (複数の文字) および `?` (単一の文字)。<br/><br/>例 1: `"fileFilter": "*.log"`<br/>例 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter は FileShare 入力データセットに適用されます。 このプロパティは、HDFS ではサポートされません。 |いいえ |
| partitionedBy |partitionedBy を使用して時系列データに動的な folderPath と fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath がパラメーター化されます。 |いいえ |
| BlobSink の format | 次のファイル形式がサポートされます: **TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](#specifying-textformat)、[Json Format](#specifying-jsonformat)、[Avro Format](#specifying-avroformat)、[Orc Format](#specifying-orcformat)、[Parquet Format](#specifying-parquetformat) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は **GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。サポートされるレベルは **Optimal** と **Fastest** です。 詳細については、「[圧縮の指定](#specifying-compression)」セクションを参照してください。 |いいえ |
| useBinaryTransfer |バイナリ転送モードを使用するかどうかを指定します。 バイナリ モードの場合は true、ASCII モードの場合は false です。 既定値: true。 このプロパティを使用できるのは、関連するリンクされたサービスの種類が FtpServer の場合のみです。 |いいえ |

> [!NOTE]
> fileName と fileFilter は、同時に使用することができません。
>
>

### <a name="using-partionedby-property"></a>partitionedBy プロパティの使用
前のセクションで説明したように、partitionedBy を使用して時系列データに動的な folderPath、fileName を指定できます。 これは、Data Factory のマクロ、および指定したデータ スライスの論理的な期間を示す システム変数 SliceStart と SliceEnd で行うことができます。

時系列データセット、スケジュール作成、スライスの詳細については、[データセットの作成](../articles/data-factory/data-factory-create-datasets.md)、[スケジュール設定と実行](../articles/data-factory/data-factory-scheduling-and-execution.md)、および[パイプラインの作成](../articles/data-factory/data-factory-create-pipelines.md)に関する記事を参照してください。

#### <a name="sample-1"></a>サンプル 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
この例では、{Slice} は、指定された形式 (YYYYMMDDHH) で、Data Factory システム変数の SliceStart の値に置き換えられます。 SliceStart はスライスの開始時刻です。 folderPath はスライスごとに異なります。 例: wikidatagateway/wikisampledataout/2014100103 または wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>サンプル 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
この例では、SliceStart の年、月、日、時刻が folderPath プロパティと fileName プロパティで使用される個別の変数に抽出されます。
