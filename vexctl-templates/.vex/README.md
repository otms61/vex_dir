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

> cat main.openvex.json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "merged-vex-9530a6f87aedc6afa1c3e02cb2a1d89bf11700ae5825113dc35902ce3274eb81",
  "author": "Unknown Author",
  "timestamp": "2024-04-22T10:00:30.672461+09:00",
  "version": 1,
  "statements": []
}

> vexctl add --in-place --document main.openvex.json --product "pkg:golang/github.com/aquasecurity/trivy" --subcomponents "pkg:golang/github.com/hashicorp/go-getter" --vuln CVE-2024-3817 --status not_affected --justification="vulnerable_code_not_in_execute_path"
 > VEX document written to main.openvex.json

> vexctl add --in-place --document main.openvex.json --product "pkg:golang/github.com/aquasecurity/trivy" --subcomponents "pkg:golang/helm.sh/helm/v3" --vuln CVE-2019-25210 --status not_affected --justification="vulnerable_code_not_in_execute_path"
 > VEX document written to main.openvex.json

> cat main.openvex.json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "https://openvex.dev/docs/public/vex-37d7a4ce42610e5c14c0749caa80fe7ab6488ff4cfbb2d518a1970f4e9e95bb8",
  "author": "vexctl (automated template)",
  "timestamp": "2024-04-22T10:35:32.878829+09:00",
  "last_updated": "2024-04-22T10:36:11.835546+09:00",
  "version": 3,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2024-3817"
      },
      "timestamp": "2024-04-22T10:35:56.915481+09:00",
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
      "timestamp": "2024-04-22T10:36:11.835547+09:00",
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

> cd ../../

> vexctl generate --templates=".openvex/templates/" --product="pkg:golang/github.com/aquasecurity/trivy@v0.50.1" > trivy_v0.50.1.openvex.json

> vexctl generate --templates=".openvex/templates/" --product="pkg:golang/github.com/aquasecurity/trivy@v0.50.0" > trivy_v0.50.0.openvex.json

> diff trivy_v0.50.0.openvex.json trivy_v0.50.1.openvex.json
5c5
<   "timestamp": "2024-04-22T10:47:09.942124+09:00",
---
>   "timestamp": "2024-04-22T10:47:04.512579+09:00",

{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "merged-vex-e573ae5e638448099a990531ab576bf2afb2a05644f003863517743bb94c6612",
  "author": "Unknown Author",
  "timestamp": "2024-04-22T10:47:04.512579+09:00",
  "version": 1,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2019-25210"
      },
      "timestamp": "2024-04-22T10:46:22.263576+09:00",
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
      "timestamp": "2024-04-22T10:46:15.87272+09:00",
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

> cd .openvex/templates/

> vexctl create --file oci.openvex.json --product "pkg:oci/aquasec/trivy" --subcomponents "pkg:golang/helm.sh/helm/v3" --vuln CVE-2019-25210 --status not_affected --justification="vulnerable_code_not_in_execute_path"
 > VEX document written to oci.openvex.json

> cd ../../

> vexctl generate --templates=".openvex/templates/" --product="pkg:oci/aquasec/trivy@v0.50.1" > oci_trivy_v0.50.1.openvex.json

> cat oci_trivy_v0.50.1.openvex.json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "merged-vex-b4560adfb8dd22b88f0ddb0fa505f2d6cf247f82e6d1489028106424d59f5f08",
  "author": "Unknown Author",
  "timestamp": "2024-04-22T10:49:33.707497+09:00",
  "version": 1,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2019-25210"
      },
      "timestamp": "2024-04-22T10:48:52.077883+09:00",
      "products": [
        {
          "@id": "pkg:oci/aquasec/trivy",
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



