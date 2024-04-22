vexctlのtemplate機能を使って、各バージョンごとにファイルを生成する。

バージョン指定をしないpurlをtemplateとして準備しておき（golden templateと呼ぶ）、各バージョンごとのvex生成を手助けしてくれる様子。


help
https://github.com/openvex/vexctl/blob/2e12d7f1f4775eaa53c9e967ab661b0dc9c96c93/internal/cmd/generate.go#L77



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
