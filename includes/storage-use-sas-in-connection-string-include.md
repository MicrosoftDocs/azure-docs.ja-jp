ストレージ アカウント内のリソースへのアクセスを許可する Shared Access Signature (SAS) の URL を所有している場合は、その SAS を接続文字列の中で使用できます。 SAS では、要求を認証するために必要な情報が URI に追加されます。リソースにアクセスするために必要な資格情報、サービス エンドポイント、プロトコルは、SAS の URI によって得られます。

共有アクセス署名を含む接続文字列を作成するには、文字列を次の形式で指定します。

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

サービス エンドポイントはいずれも省略可能ですが、少なくとも 1 つは、接続文字列に存在する必要があります。

> [!NOTE]
> ベスト プラクティスとして、SAS は HTTPS と組み合わせて使用することをお勧めします。
>
> 構成ファイル内で接続文字列に SAS を指定する場合、URL に含まれる特殊文字のエンコードが必要になる場合があります。
>
>

### <a name="service-sas-example"></a>サービス SAS の例
Blob Storage のサービス SAS を含んだ接続文字列の例を次に示します。

```
BlobEndpoint=https://storagesample.blob.core.windows.net;SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

そしてこちらが、同じ接続文字列に特殊文字のエンコードを適用した例です。

```
BlobEndpoint=https://storagesample.blob.core.windows.net;SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>アカウント SAS の例
Blob Storage と File Storage に使用されるアカウントの SAS を含んだ接続文字列の例を次に示します。 両方のサービスのエンドポイントが指定されていることに注意してください。

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

そしてこちらが、同じ接続文字列に URL エンコードを適用した例です。

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

