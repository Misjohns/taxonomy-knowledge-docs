# OCP-V検証項目

## 前提条件
- ODFがデプロイ済み

## OpenShift Virtualization Operatorのデプロイ
### OpenShift Virutalizatio Operatorのインストール
https://access.redhat.com/documentation/ja-jp/openshift_container_platform/4.14/html-single/virtualization/index#installing-virt-operator_installing-virt

## VMの作成
### 新規Projectの作成
新規VM検証用のProjectを作成
### テンプレートからの新規RHEL-VM作成
https://access.redhat.com/documentation/ja-jp/openshift_container_platform/4.14/html-single/virtualization/index#virt-creating-vm-from-template_virt-creating-vms-from-templates

https://github.com/tutsunom/roadshow_ocpvirt_instructions/blob/main-jp/workshop/content/03_ocpv_basics.adoc#%E3%83%86%E3%83%B3%E3%83%97%E3%83%AC%E3%83%BC%E3%83%88%E3%82%92%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%9E%E3%82%A4%E3%82%BA%E3%81%97%E3%81%A6-linux-vm-%E3%82%92%E4%BD%9C%E6%88%90

### VMへのコンソールからのアクセス
https://access.redhat.com/documentation/ja-jp/openshift_container_platform/4.14/html-single/virtualization/index#vnc-console_virt-accessing-vm-consoles

https://github.com/tutsunom/roadshow_ocpvirt_instructions/blob/main-jp/workshop/content/03_ocpv_basics.adoc#%E4%BB%AE%E6%83%B3%E3%83%9E%E3%82%B7%E3%83%B3%E3%82%B3%E3%83%B3%E3%82%BD%E3%83%BC%E3%83%AB%E3%81%B8%E3%81%AE%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9

### VMへのアプリケーションのインストール
- httpdをDNFでインストールし、適当にPort:80で立ち上がるようにしておく
- Note: RHELだとsubscription-manager registerしておかないとRepoが見れないので、CentOSでもいいかも？

## 既存VMの取り込み
既存VMware環境からVMDKイメージをqcow2で取り込む想定
### 新規Projectの作成
既存VM検証用のProjectを作成
### 既存イメージからの新規VMの作成
https://access.redhat.com/documentation/ja-jp/openshift_container_platform/4.14/html-single/virtualization/index#virt-creating-vm-custom-image-web_virt-creating-vms-from-container-disks
### VMへのコンソールからのアクセス
https://access.redhat.com/documentation/ja-jp/openshift_container_platform/4.14/html-single/virtualization/index#vnc-console_virt-accessing-vm-consoles

https://github.com/tutsunom/roadshow_ocpvirt_instructions/blob/main-jp/workshop/content/03_ocpv_basics.adoc#%E4%BB%AE%E6%83%B3%E3%83%9E%E3%82%B7%E3%83%B3%E3%82%B3%E3%83%B3%E3%82%BD%E3%83%BC%E3%83%AB%E3%81%B8%E3%81%AE%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9

## K8s NetworkからのVMへのアクセス
VM上でhttpdをホストしておく
  
https://github.com/tutsunom/roadshow_ocpvirt_instructions/blob/main-jp/workshop/content/12_service_route.adoc

### Serviceの作成
https://github.com/tutsunom/roadshow_ocpvirt_instructions/blob/main-jp/workshop/content/12_service_route.adoc#service-%E3%81%AE%E4%BD%9C%E6%88%90
### Clusterからのhttpdへのアクセス
curlでアクセス
### Routeの作成
https://github.com/tutsunom/roadshow_ocpvirt_instructions/blob/main-jp/workshop/content/12_service_route.adoc#route-%E3%81%AE%E4%BD%9C%E6%88%90
### クラスタ外からのhttpdへのアクセス
Browserからアクセス

## VM向けのK8s Storageの構成
### PVCの作成
StoragrClass: ocs-xxx-ceph-rbd でPVCを作成
### VMへのPVCのアタッチ
https://access.redhat.com/documentation/ja-jp/openshift_container_platform/4.14/html-single/virtualization/index#virt-hot-plugging-disks-ui_virt-hot-plugging-virtual-disks

## 高度なVM管理
### VMのスナップショット
https://github.com/tutsunom/roadshow_ocpvirt_instructions/blob/main-jp/workshop/content/09_storage_management.adoc#%E3%82%B9%E3%83%8A%E3%83%83%E3%83%97%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88
### VMのクローン作成
https://github.com/tutsunom/roadshow_ocpvirt_instructions/blob/main-jp/workshop/content/09_storage_management.adoc#%E4%BB%AE%E6%83%B3%E3%83%9E%E3%82%B7%E3%83%B3%E3%81%AE%E3%82%AF%E3%83%AD%E3%83%BC%E3%83%B3
### VMのLive Migration
https://github.com/tutsunom/roadshow_ocpvirt_instructions/blob/main-jp/workshop/content/03_ocpv_basics.adoc#%E4%BB%AE%E6%83%B3%E3%83%9E%E3%82%B7%E3%83%B3%E3%81%AE-live-migration
## VMの高可用性
### K8s Networkを使ったVMのロードバランシング
- httpdを実行したVM x2を作成
- 上記VMをターゲットとするClusterIP Serviceを作成
- 上記ClusterIP ServiceでRouteを作成

なぜか、Load Balanceしない。。。Act/Stdbyのような動き

### Node Failure時のVMのHA
- httpdを実行したVMを作成(Eviction Strategy: LiveMigrate)
- Drain Worker Node
- httpdの実行を確認