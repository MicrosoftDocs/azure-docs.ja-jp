---
title: JAR 依存関係の管理 - Azure HDInsight
description: この記事では、HDInsight アプリケーションの Java アーカイブ (JAR) 依存関係を管理するためのベスト プラクティスについて説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: e3807f54de672642bfc6454cf38babbf189c210a
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086077"
---
# <a name="jar-dependency-management-best-practices"></a>JAR 依存関係の管理のベスト プラクティス

HDInsight クラスターにインストールされているコンポーネントには、サードパーティ製ライブラリへの依存関係があります。 通常、これらの組み込みコンポーネントによって、特定のバージョンの共通モジュール (Guava など) が参照されます。 その依存関係があるアプリケーションを送信すると、同じモジュールの異なるバージョンとの間で競合が発生する可能性があります。 最初にクラスパスでコンポーネントのバージョンを参照した場合、バージョンの非互換性により、組み込みコンポーネントによって例外がスローされる可能性があります。 しかし、最初に組み込みコンポーネントによってクラスパスに依存関係が挿入された場合は、アプリケーションから `NoSuchMethod` のようなエラーがスローされる可能性があります。

バージョンの競合を回避するには、アプリケーションの依存関係のシェーディングを検討してください。

## <a name="what-does-package-shading-mean"></a>パッケージのシェーディングとは
シェーディングとは、依存関係を含めたり、その名前を変更したりする方法を指します。 クラスを再配置し、影響を受けるバイトコードとリソースを書き換えて、ご自分の依存関係のプライベート コピーを作成します。

## <a name="how-to-shade-a-package"></a>パッケージをシェーディングする方法

### <a name="use-uber-jar"></a>uber-jar を使用する
uber-jar は、アプリケーション jar とその依存関係の両方を含む単一の jar ファイルです。 uber-jar の依存関係は、既定ではシェーディングされていません。 他のコンポーネントまたはアプリケーションによって別のバージョンのライブラリが参照されると、場合によってはバージョンの競合が発生することがあります。 これを回避するには、一部 (またはすべて) の依存関係がシェーディングされた uber-jar ファイルをビルドします。

### <a name="shade-package-using-maven"></a>Maven を使用してパッケージをシェーディングする
Maven では、Java と Scala の両方で記述されたアプリケーションをビルドできます。 maven-shade-plugin を使用すると、シェーディングされた uber-jar を簡単に作成できます。

以下の例で示すのは、maven-shade-plugin を使用してパッケージをシェーディングするよう更新されたファイル `pom.xml` です。  XML セクション `<relocation>…</relocation>` では、対応する JAR ファイルのエントリを移動させ、影響を受けるバイトコードを書き換えることによって、パッケージ `com.google.guava` からパッケージ `com.google.shaded.guava` にクラスを移動させます。

`pom.xml` の変更後、`mvn package` を実行して、シェーディングされた uber-jar をビルドできます。

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>SBT を使用してパッケージをシェーディングする
SBT も、Scala および Java 向けのビルド ツールです。 SBT には、maven-shade-plugin のようなシェーディング用プラグインはありません。 `build.sbt` ファイルを変更することでパッケージをシェーディングできます。 

たとえば、`com.google.guava` をシェーディングするには、`build.sbt` ファイルに次のコマンドを追加します。

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

次に、`sbt clean` と `sbt assembly` を実行して、シェーディングされた jar ファイルをビルドできます。 

## <a name="next-steps"></a>次のステップ

* [HDInsight IntelliJ ツールを使用する](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [IntelliJ で Spark 用の Scala Maven アプリケーションを作成する](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
