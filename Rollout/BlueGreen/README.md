# BlueGreen Deployment PoC
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) 
## 本資料の目的
- ArgoCD [Rollout](https://argoproj.github.io/argo-rollouts/features/bluegreen/) CRDによるデプロイメント戦略/実装の一部を共有
   - ArgoCDは目的を達成する為の手段でしかない
- Rolloutの内、BlueGreen戦略/機能の基本的な「実装」を共有
- シナリオ4.　について人の手を介して行われる一連のk8s的な操作をArgoCDに置き換えていく
- 結果としてシステム開発(特にコーディング)に注力する事になり、インフラ管理から開放され、リリースタスク工数の削減等が本資料が目指す所

## シナリオ
- 本来はCI/CD戦略ありきで進めるはずで、疑問点が諸所あろうとは思いますがご容赦願いたい

1. kubernetes上にすでに稼働中のService, Deployment, StatefulSetがあり稼働しているものとする
2. 上記の[アプリケーション/サービス](gcr.io/heptio-images/ks-guestbook-demo)は特定のRepository、Branchで管理されており
 [Ver0.1](https://github.com/isfukuda/ArgoCD/commit/2c3d27f7a27a5d57625bf0db70ccc432b42b1ee0) -> [0.2](https://github.com/isfukuda/ArgoCD/commit/b9a5258a89423821b45a6052b2c810eafcde016f) にあげる
5. Ver0.2をリリースする[Manifest](https://github.com/isfukuda/ArgoCD/blob/main/Rollout/BlueGreen/deployment.yml)が所定のBranchへpush、MainブランチへPullReqが走り承認プロセスが回り[Merge](https://github.com/isfukuda/ArgoCD/blob/main/Rollout/BlueGreen/pullrequest.png)されるとする
6. ArgoCDがMainブランチの変更を検知、BlueGreenデプロイメントをManifestに従い自動実行される

## 実装結果
- ArgoCD UIのスクショを中心に共有
### Argo, APP登録
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
- Project毎(あるいはService)リリース要件のガイドラインが決まり次第、改めて検証が必須になるのでご留意ください
- ArgoCDは多機能で触れていない事が多数あるのでこれがArgoCDの全てではない点、誤解のないようにお願いします
- 本資料に書かれてない事は「考慮されていない事」です
- ArgoCD RollOut[公式動画](https://www.youtube.com/watch?v=hIL0E2gLkf8)
- BluleGreen Deployment [解説動画](https://www.youtube.com/watch?v=krDxDz4V4Tg)
## 触れなかった事
1. CI/CD全般
2. アプリケーションとそのImage構築、管理
3. アプリケーションリリースに関するテスト全般
4. Git/Branch戦略
5. k8s
6. MicroService Architecture全般　
7. Database連携
8. 監視全般、とその連携
9. Monitoring
