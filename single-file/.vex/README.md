
openvexのファイルはひとつで、addしていく。
ファイル名はこのディレクトリ構成を参考に、openvex.json としている。
https://github.com/openvex/vexctl/tree/7d1a1e89d4fbfe35ddb81538959f64ca595e8806/examples/csaf


```bash
> vexctl create --file openvex.json \
          --product "pkg:golang/github.com/aquasecurity/trivy" \
          --subcomponents "pkg:golang/github.com/hashicorp/go-getter" \
          --vuln CVE-2024-3817 \
          --status not_affected \
          --author "The Trivy Project" \
          --justification="vulnerable_code_not_in_execute_path"

> vexctl add --in-place --document openvex.json \
          --product "pkg:golang/github.com/aquasecurity/trivy" \
          --subcomponents "pkg:golang/helm.sh/helm/v3" \
          --vuln CVE-2019-25210 \
          --status not_affected \
          --justification="vulnerable_code_not_in_execute_path"

> cat openvex.json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "https://openvex.dev/docs/public/vex-738dc347c1d33ae81ea0d17c3c2ad8d57b61645b24fa712a098972f4d9845ae7",
  "author": "The Trivy Project",
  "timestamp": "2024-04-22T10:34:19.515641+09:00",
  "last_updated": "2024-04-22T10:34:26.41617+09:00",
  "version": 2,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2024-3817"
      },
      "timestamp": "2024-04-22T10:34:19.515641+09:00",
      "products": [
        {
          "@id": "pkg:golang/github.com/aquasecurity/trivy",
          "subcomponents": [
            {
              "@id": "pkg:golang/github.com/hashicorp/go-getter"
            }
          ]
        }
      ],
      "status": "not_affected",
      "justification": "vulnerable_code_not_in_execute_path"
    },
    {
      "vulnerability": {
        "name": "CVE-2019-25210"
      },
      "timestamp": "2024-04-22T10:34:26.41617+09:00",
      "products": [
        {
          "@id": "pkg:golang/github.com/aquasecurity/trivy",
          "subcomponents": [
            {
              "@id": "pkg:golang/helm.sh/helm/v3"
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
