author がそれぞれ変わる時に便利なのかもしれない。
ただし、一つのファイルにしたい時には、結局authorをどうするのかという問題は発生するので、authorの自由度が高いことのメリットは少ないかもしれない。

```
> vexctl create --product="pkg:golang/github.com/aquasecurity/trivy" \
                                       --vuln="CVE-2024-3817" \
                                       --status="not_affected" \
                                       --justification="vulnerable_code_not_in_execute_path" \
                                       --subcomponents="pkg:golang/github.com/hashicorp/go-getter" \
                                       --author "Author A" \
                                       --file CVE-2024-3817.openvex.json

> vexctl create --product="pkg:golang/github.com/aquasecurity/trivy" \
                                 --vuln="CVE-2019-25210" \
                                 --status="not_affected" \
                                 --justification="vulnerable_code_not_in_execute_path" \
                                 --subcomponents="pkg:golang/helm.sh/helm/v3" \
                                 --author "Author B" \
                                 --file CVE-2019-25210.openvex.json
```


merge
```bash
> vexctl merge --author "The Trivy Project" \
    --product="pkg:golang/github.com/aquasecurity/trivy" \
    *.openvex.json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "merged-vex-4319e213818c55d1e3a2df5ef8a5d6f37eeb47eb4eca648ccb7fad1ce20c7009",
  "author": "The Trivy Project",
  "timestamp": "2024-04-22T20:13:12.499963+09:00",
  "version": 1,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2019-25210"
      },
      "timestamp": "2024-04-22T13:06:59.586359+09:00",
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
    },
    {
      "vulnerability": {
        "name": "CVE-2024-3817"
      },
      "timestamp": "2024-04-22T13:05:31.319261+09:00",
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
    }
  ]
}
```