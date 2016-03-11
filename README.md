#静的HTML公開フローをサーバレスでDevOps！[本編]
##ハンズオン用テキスト  
##実施日: 2016/03/12

##情報
- IAM
   - User Name : jawsdays2016-s3-deploy-user
   - Policy Name : jawsdays2016-s3-deploy-policy
   - Access Key Id : ＜取得したAccess Key Id＞
   - Secret Access Key : ＜取得した Secret Access Key＞

- S3
   - 本番バケット名 : prod.＜GitHubアカウント＞-jawsdays2016.jp
   - ステージングバケット名 : stg.＜GitHubアカウント＞-jawsdays2016.jp
   - リージョン : Tokyo

- GitHub
   - Fork先リポジトリ名 : [https://github.com/proudit/jawsdays-samplesite](https://github.com/proudit/jawsdays-samplesite)

　
　　
　　

##S3 Bucket Policyサンプル


1-7. 本番用バケットポリシーの設定② 

```
{
  "Version":"2012-10-17",
  "Statement":[{
    "Sid":"AddPerm",
        "Effect":"Allow",
      "Principal": "*",
      "Action":["s3:GetObject"],
      "Resource":["arn:aws:s3:::＜本番バケット名＞/*"
      ]
    }
  ]
}
```

1-9. ステージング用バケットポリシーの設定②

```
{
  "Version":"2012-10-17",
  "Statement":[{
    "Sid":"AddPerm",
        "Effect":"Allow",
      "Principal": "*",
      "Action":["s3:GetObject"],
      "Resource":["arn:aws:s3:::＜ステージングバケット名＞/*"
      ]
    }
  ]
}
```

　
　
##IAMポリシーサンプル

2-7. IAMポリシー作成③

```
{
"Version": "2012-10-17",
"Statement": [
    {
        "Effect": "Allow",
        "Action": [
            "s3:GetObject",
            "s3:PutObject",
            "s3:DeleteObject",
            "s3:ListBucket"
        ],
        "Resource": [
            "arn:aws:s3:::＜本番バケット名＞",
            "arn:aws:s3:::＜本番バケット名＞/*",
            "arn:aws:s3:::＜ステージングバケット名＞",
            "arn:aws:s3:::＜ステージングバケット名＞/*"
        ]
    }
]
}
```

　
　

##circle.yml　サンプル

```
machine:
  timezone:
    Asia/Tokyo

dependencies:
    override:
        - sudo pip install awscli
    post:
        - aws configure set region ap-northeast-1

test:
  override:
    - echo "All the tests are passed!!"

deployment:
  production:
    branch: master
    commands:
      - aws s3 sync html/ s3://＜本番バケット名＞/ --delete
  staging:
    branch: staging
    commands:
      - aws s3 sync html/ s3://＜ステージングバケット名＞/ --delete
```
