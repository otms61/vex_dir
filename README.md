## 要約

レポジトリ内でopenvexのファイルを管理する構成

- defaultブランチの `${repo root}/.vex` をVEXファイルの置き場とする
- ファイル名は、 `openvex.json` か、 `.vex.json` か`.openvex.json` という拡張子を持ったファイルとする
- 複数のopenvexファイルを持つ場合は、`${repo root}/.vex` 直下に置く


## 構成
githubでのデフォルトブランチの .vex ディレクトリをopenvexファイルの置き場とする。同じディレクトリには別フォーマットのvexが置かれる可能性などもあるかもしれない。

> [!NOTE]
> ディレクトリ名は `.vex` で良いのか？ 別案としては `.openvex` など。
>  `.vex/openvex` みたいなのも考えられるが、openvexであることは拡張子で明らかにしておけば、ディレクトリを掘るまでもないとは思う。

ファイル名は、`openvex.json` か、拡張子に `.vex.json` or `.openvex.json` とする。

vexctl内には `openvex.json` というファイル名と、二つ拡張子( `.openvex.json` と `.vex.json` )が使われている。
- `openvex.json`
  - https://github.com/openvex/vexctl/tree/main/examples/csaf
- `xxx.vex.json`
  - https://github.com/openvex/vexctl/tree/main/examples/openvex
- `xxx.openvex.json`
  - https://github.com/openvex/vexctl/tree/main/examples/sarif

コマンドのヘルプやレポジトリ内のexamplesのファイルを見ると、`openvex.json` か `.openvex.json` が使われていることが多いことが多い。

> [!NOTE]
> OpenVEXの仕様としては、拡張子にルールはなく、JSON-LDの `@context` の設定がされていれば良い。
> 
> https://github.com/openvex/spec/blob/main/OPENVEX-SPEC.md#openvex-and-json-ld
> ただ、複数ファイル置かれていたりするので、パーサーを作る時に余計なファイルを読み込まなくて済む点や、vexctlなどのツールを使う時に拡張子として拡張子をつけていた方が扱いやすそう。  `vexctl merge *.openvex.json` みたいにかける。

## statementのマッチングについて

マッチングの方針
- openvexのファイルが複数に分かれていた場合には、全てをマージする
- vex-goのマッチングにより、マッチするステートメント一覧を取得する
  - 複数マッチした場合には、バージョンなどの指定まで一致していれば、そちらを優先。
  - [細かく指定したvexを置きたい場合の例](./complex-situation/.vex) を参照

## 例

以下の

### 一番シンプルな構成(single-file/)

[single fileの例](./single-file/.vex)

`.vex/openvex.json` の一ファイル

除外したいCVEがでるごとに、 `vexctl add` していく

### もう少し細かく指定したvexを置きたい構成(complex-situation/)

[細かく指定したvexを置きたい場合の例](./complex-situation/.vex)

ひとつのレポジトリに二つのプロダクトが管理されている場合。
また、特定のバージョンだけ追加でステートメントを置きたい場合。

```
.
└── .vex
    ├── README.md
    ├── other-cmd-in-trivy-repo.openvex.json
    ├── other-cmd-in-trivy-repo@v0.0.2.openvex.json
    └── trivy.openvex.json
```

### CVEごとにopenvexファイルを生成する構成（multi-files-per-cve）

[CVEごとにopenvexファイルを生成する例](./multi-files-per-cve/.vex)

可能性は低いが、CVEごとに追加していきたいというのはあるかもしれない。

```
> tree . -a
.
└── .vex
    ├── CVE-2019-25210.openvex.json
    ├── CVE-2024-3817.openvex.json
    └── README.md
```


## 要検討項目

### Authorどうするのか問題。

仕様
> Author is the identifier for the author of the VEX statement. This field should ideally be a machine readable identifier such as an IRI, email address, etc. author MUST be an individual or organization. author identity SHOULD be cryptographically associated with the signature of the VEX document or other exchange mechanism.

createしていく運用ならそこまで問題にならないかも。
addの運用の時には一つ前のauthorが実作業者と違う場合などをこうりょする必要ありそう。add運用の時には組織名を使うとかにはなりそう。

```
https://github.com/openvex/spec/blob/main/OPENVEX-SPEC.md#status-justifications
  "author": "Wolfi J Inkinson",
  "author": "Spring Builds <spring-builds@users.noreply.github.com>",
```

```
https://github.com/openvex/vexctl/blob/2e12d7f1f4775eaa53c9e967ab661b0dc9c96c93/examples/sarif/sample-1statement.openvex.json#L4
  "author": "The OpenVEX Project",
```

### 別のpurlも起きたい時にはどうすれば良いのか？

`pkg:golang/github.com/aquasecurity/trivy` について検討をしているが、 `pkg:oci/aqua/trivy` についてのvexファイルはどう管理するのが良いか？
同じファイルに複数プロダクトを書くのか、ファイル名を分けるのか？
