# BlueGreen Deployment PoC
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) 
## 目的
- ArgoCD [Rollout](https://argoproj.github.io/argo-rollouts/features/bluegreen/) CRDによるデプロイメント戦略/実装の一部を共有
- BlueGreen戦略の基本的な「実装」の共有

## 本件のシナリオ
- 本来はCI/CD戦略ありきで進めるはずであり、疑問点が諸所あろうとは思いますがご容赦願います
- 【POINT】下記の4.について人の手を介して行われる一連のk8s的な操作をArgoCDに置き換えたい

1. kubernetes上にすでに稼働中のService, Deployment, StatefulSetがあり稼働している、とする
2. 上記のアプリケーション/サービスは特定のRepository、Branchで管理されている
3. 今回はk8s上の特定の「サービス/機能」に関して Ver0.1 -> 0.2 にあげる
4. Ver0.2をリリースするManifestが所定のBranchへpush、MainブランチへPullReqが走り承認プロセスが回る、Mergeされる
6. ArgoCDがMainブランチの変更を検知、BlueGreenデプロイメントをManifestに従い自動実行される
## 実装結果
- ArgoCD UIのスクショ
### Argo, APP NEW
- ArgoCD UI上に"APP"を作成し、Repo等の情報を入力
<img width="550" src="https://github.com/isfukuda/ArgoCD/blob/main/Rollout/BlueGreen/APP_NEW_CREATE.png">
<img width="550" src="https://github.com/isfukuda/ArgoCD/blob/main/Rollout/BlueGreen/APP_NEW_CREATE_2nd.png"> 
### k8s状態確認

- APPにより、k8s上の各種リソースオブジェクトの状況把握
<img width="550" src="https://github.com/isfukuda/ArgoCD/blob/main/Rollout/BlueGreen/APP_NEW_STATUS.png">
### Git push, merge
<img width="550" src="https://github.com/isfukuda/ArgoCD/blob/main/Rollout/BlueGreen/pullrequest.png">
### BlueGreen Deployment
- 画像一番下、新しいPOD(RS:ReplicaSet)が出現、古いPODと入れ替わる
<img width="550" src="https://github.com/isfukuda/ArgoCD/blob/main/Rollout/BlueGreen/bluegreen_result.png">

## 備考/補足
- OB3リリース要件のガイドラインが決まり次第、改めて検証が必須になるのでご留意ください
- ArgoCDは多機能で触れていない事が多数あるので誤解のないようにお願いします
- 本資料に書かれてない事は「考慮されていない事」です
### 触れなかった事
1. CI/CD全般
2. アプリケーションとそのImage構築、管理
3. アプリケーションリリースに関するテスト全般
4. Git/Branch戦略
5. k8s
6. MicroService Architecture全般　
7. Database連携
