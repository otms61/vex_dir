現実的にありそうな、複雑なレポジトリと状況を検討するディレクトリ


### 一つのレポジトリに複数プロダクトがある場合

`github.com/aquasecurity/trivy/cmd/trivy/main.go`
だけでなく、
`github.com/aquasecurity/trivy/cmd/other-cmd-in-trivy-repo/main.go`
もあり、それを管理したい場合。
`trivy.openvex.json` と `other-cmd-in-trivy-repo.openvex.json` などでわける。


以下のような複数のproductをもつvexファイルはありうるが、扱いがやや面倒ではある。

CVE-2019-25210.openvex.json

```json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "https://openvex.dev/docs/public/vex-0f107e3acc34096ac0e4aa08b91740ba2b9df954b6c1ed5e001c52691b156b7e",
  "author": "Author B",
  "timestamp": "2024-04-22T13:06:59.586359+09:00",
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
        },
        {
          "@id": "pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo",
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

```json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "https://openvex.dev/docs/public/vex-0f107e3acc34096ac0e4aa08b91740ba2b9df954b6c1ed5e001c52691b156b7e",
  "author": "Author B",
  "timestamp": "2024-04-22T13:06:59.586359+09:00",
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
        "name": "CVE-2019-25210"
      },
      "timestamp": "2024-04-22T13:06:59.586359+09:00",
      "products": [
        {
          "@id": "pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo",
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

### 特定のバージョンでだけvexを用意したい場合

other-cmd-in-trivy-repoというツールにおいて、以下のシチュエーションを考える。

- other-cmd-in-trivy-repoは、v0.0.1, v0.0.2, v0.0.3という3つのバージョンがある
- other-cmd-in-trivy-repoの全てのバージョンで、github.com/otms61/false-positive-vuln@v0.0.1 というライブラリが誤検出される
- github.com/otms61/vuln@0.0.1 には脆弱性があり、other-cmd-in-trivy-repo@v0.0.1とother-cmd-in-trivy-repo@v0.0.2 で使用している
- other-cmd-in-trivy-repo@v0.0.1 では脆弱性の影響を受ける
  - true positive
- other-cmd-in-trivy-repo@v0.0.2 では脆弱性のライブラリを使ったままだが、脆弱性のある関数を使わないようにした
  - false positive
  - v0.0.2のみvexを用意したい。全バージョンにしてしまうと、other-cmd-in-trivy-repo@v0.0.1で false negativeとなってしまう。
- github.com/otms61/vuln@0.0.2 では脆弱性の修正が含まれている
  - true negative


pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo


```bash
> tree . -a
.
└── .vex
    ├── other-cmd-in-trivy-repo.openvex.json
    ├── other-cmd-in-trivy-repo@v0.0.2.openvex.json
    └── trivy.openvex.json

2 directories, 3 files

> cat .vex/other-cmd-in-trivy-repo.openvex.json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "https://openvex.dev/docs/public/vex-d625b0c7e6d8c3ab0ee7f6bd77efad8a1688ff5ab7c77ed3c7a839ad5225da83",
  "author": "Unknown Author",
  "timestamp": "2024-04-22T09:50:22.925218+09:00",
  "version": 1,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2024-1111"
      },
      "timestamp": "2024-04-22T09:50:22.925219+09:00",
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

> cat .vex/other-cmd-in-trivy-repo@v0.0.2.openvex.json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "https://openvex.dev/docs/public/vex-d625b0c7e6d8c3ab0ee7f6bd77efad8a1688ff5ab7c77ed3c7a839ad5225da83",
  "author": "Unknown Author",
  "timestamp": "2024-04-22T09:50:22.925218+09:00",
  "version": 1,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2024-2222"
      },
      "timestamp": "2024-04-22T09:50:22.925219+09:00",
      "products": [
        {
          "@id": "pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo@v0.0.2",
          "subcomponents": [
            {
              "@id": "pkg:golang/github.com/otms61/vuln@0.0.1"
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

### ひとつのファイルにマージしたい場合

コンテナイメージにreferrerを付与したり、attestを用意したり、リリースページに含めたい場合などに、ひとつのファイルにまとめたい可能性はある。
vexctl mergeがバージョンごとのフィルターを用意してくれているため、ひとつのファイルにしたい時にはこれを使える。


```bash
> vexctl merge --product="pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo@v0.0.1" *.openvex.json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "merged-vex-7f046b9fa3f6e48f7a35fee55df7704d97aa0a66e2cdb984102d036c24c99c89",
  "author": "Unknown Author",
  "timestamp": "2024-04-22T14:16:47.297261+09:00",
  "version": 1,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2024-1111"
      },
      "timestamp": "2024-04-22T09:50:22.925219+09:00",
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

> vexctl merge --product="pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo@v0.0.2" *.openvex.json
{
  "@context": "https://openvex.dev/ns/v0.2.0",
  "@id": "merged-vex-7f046b9fa3f6e48f7a35fee55df7704d97aa0a66e2cdb984102d036c24c99c89",
  "author": "Unknown Author",
  "timestamp": "2024-04-22T14:16:29.432457+09:00",
  "version": 1,
  "statements": [
    {
      "vulnerability": {
        "name": "CVE-2024-1111"
      },
      "timestamp": "2024-04-22T09:50:22.925219+09:00",
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
    },
    {
      "vulnerability": {
        "name": "CVE-2024-2222"
      },
      "timestamp": "2024-04-22T09:50:22.925219+09:00",
      "products": [
        {
          "@id": "pkg:golang/github.com/aquasecurity/other-cmd-in-trivy-repo@v0.0.2",
          "subcomponents": [
            {
              "@id": "pkg:golang/github.com/otms61/vuln@0.0.1"
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
