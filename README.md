# Ansible学習用CloudFormationテンプレート

## 概要
このリポジトリには、AWS上にAnsibleの検証環境（マスター/ターゲット構成）を自動構築するCloudFormationテンプレート `ansible_gakusyuu.yaml` が含まれています。

- **Ansible Masterサーバ**（EC2, SSM接続, Ansibleインストール済み）
- **ターゲットサーバ**（EC2, SSH/ICMPはMasterからのみ許可）
- **VPC/サブネット/セキュリティグループ**などのネットワークリソース

## ファイル一覧
- `ansible_gakusyuu.yaml` : 本テンプレートファイル

## 使い方
1. **事前準備**
   - AWSアカウントと管理者権限
   - EC2キーペア（KeyPairNameパラメータで指定）
2. **CloudFormationスタックの作成**
   - AWSマネジメントコンソールまたはAWS CLIで`ansible_gakusyuu.yaml`をアップロードし、スタックを作成
   - パラメータ`KeyPairName`に既存のキーペア名を指定
3. **デプロイ後**
   - 出力値`AnsibleMasterInstanceId`からMasterサーバのインスタンスIDを確認
   - SSM Session Manager経由で接続し、Ansibleコマンド等を実行

## パラメータ
| パラメータ名    | 説明                                      |
|----------------|-------------------------------------------|
| KeyPairName    | EC2インスタンスに割り当てる既存キーペア名 |

## 主なリソース構成
- **VPC**: 10.0.0.0/16
- **PublicSubnet**: 10.0.1.0/24（Master/ターゲット配置）
- **AnsibleMasterInstance**: t3.micro, Amazon Linux, SSM/Ansible/ユーザ作成済み
- **AnsibleDevTarget1/2, AnsibleTestTarget1/2**: t3.micro, Amazon Linux
- **セキュリティグループ**: Master→ターゲットのみSSH/ICMP許可
- **IAMロール/インスタンスプロファイル**: SSM用

## 注意事項
- テンプレート内のUserDataでansibleユーザや秘密鍵、インベントリファイル等を自動作成します
- セキュリティのため、秘密鍵の管理やアクセス権限に注意してください
- テンプレートや構成の詳細は`ansible_gakusyuu.yaml`を参照してください
