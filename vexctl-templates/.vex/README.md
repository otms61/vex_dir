vexctlのtemplate機能を使う方法。
templateの効果的な使い方はピンときていないが、各バージョンごとにファイルを生成する方法を考えてみる。

templateは、バージョン指定をしないpurlをtemplateとして準備しておき（golden templateと呼んでいる）、各バージョンごとのvex生成を手助けしてくれる様子。

このコマンドのhelpのドキュメント: https://github.com/openvex/vexctl/blob/2e12d7f1f4775eaa53c9e967ab661b0dc9c96c93/internal/cmd/generate.go#L77


```bash
> vexctl generate --init
INFO Initialized new templates directory in .openvex/templates

> cd .openvex/templates/

> ls
README.md          main.openvex.json

> vexctl create --file other-cmd-in-trivy-repo.openvex.json \
                --product "pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo" \
                --subcomponents "pkg:golang/github.com/otms61/false-positive-vuln@v0.0.1" \
                --vuln CVE-2024-1111 \
                --status not_affected \
                --author "The Trivy Project" \
                --justification="vulnerable_code_not_in_execute_path"

> vexctl create --file other-cmd-in-trivy-repo@v0.0.2.openvex.json \
                --product "pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo@v0.0.2" \
                --subcomponents "pkg:golang/github.com/otms61/vuln@v0.0.1" \
                --vuln CVE-2024-2222 \
                --status not_affected \
                --author "The Trivy Project" \
                --justification="vulnerable_code_not_in_execute_path"

> cd ../../

> vexctl generate --file other-cmd-in-trivy-repo@v0.0.1.openvex.json \
  --product="pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo@v0.0.1" \
  --templates=".openvex/templates/" \
  --author "The Trivy Project"


> vexctl generate --file other-cmd-in-trivy-repo@v0.0.2.openvex.json \
  --product="pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo@v0.0.2" \
  --templates=".openvex/templates/" \
  --author "The Trivy Project"


> vexctl generate --file other-cmd-in-trivy-repo@v0.0.3.openvex.json \
  --product="pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo@v0.0.3" \
  --templates=".openvex/templates/" \
  --author "The Trivy Project"

```


mergeよりもスマートそうには見えるが、バージョンごとにopenvex.jsonを作って管理しようとすると、.openvex/templates/other-cmd-in-trivy-repo.openvex.json にstatementを追加した場合に、再度すべてのバージョンでgenerateし直す必要がありそう。

なお、statements内のproductはテンプレートの値が使われる。

          
`"@id": "pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo",` の部分はこれのまま。`"@id": "pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo@v0.0.1",` に変えてくれたりはしない。

```
vexctl generate  \
  --product="pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo@v0.0.1" \
  --templates=".openvex/templates/" \
  --author "The Trivy Project"
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "merged-vex-87189908bada90e8765a32706200bd3f59ea2e2684c535c36d15f49cb137fec8",
  "author": "The Trivy Project",
  "timestamp": "2024-04-22T23:23:29.062949+09:00",
  "version": 1,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2024-1111"
      },
      "timestamp": "2024-04-22T22:56:53.626507+09:00",
      "products": [
        {
          "@id": "pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo",
          "subcomponents": [
            {
              "@id": "pkg:golang/github.com/otms61/false-positive-vuln@v0.0.1"
            }
          ]
        }
      ],
      "status": "not_affected",
      "justification": "vulnerable_code_not_in_execute_path"
    }
  ]
}
```