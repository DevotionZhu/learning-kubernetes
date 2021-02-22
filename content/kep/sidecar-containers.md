---
title: Sidecar Containers
type: book
date: "2021-02-02"
weight: 1
---

## 时间线

* 2018-05: [Joseph Irving](https://github.com/Joseph-Irving) 发起 Sidecar Containers 的 [KEP](https://github.com/kubernetes/community/pull/2148)
* 2018-11: KEP 被[接受](https://github.com/kubernetes/community/pull/2148#issuecomment-442991599)。
* 2019-01: 新开 issue [#753](https://github.com/kubernetes/enhancements/issues/753) 来跟进这个特性的进展。
* 2020-10: 经过两年的设计与开发，社区意见出现分歧，最终宣布该 KEP 被废弃，见作者的 [评论](https://github.com/kubernetes/enhancements/issues/753#issuecomment-713471597)。
* 2020-11: Tim Hockin 发起新 [Proposal](https://docs.google.com/document/d/1Q3685Ic2WV7jPo9vpmirZL1zLVJU91zd3_p_aFDPcS0) 草稿。

* [Sidecar use cases/requirements](https://docs.google.com/document/d/1Drw9C_Ljpcr4X9UPLvms1fn8uMRnTfJLb-xipgX4C1M/edit#heading=h.1kqwby7migh2)
* [sidecar: Address all concerns raised](https://github.com/kubernetes/enhancements/pull/1980)

## case

* istio issue: [#7136](https://github.com/istio/istio/issues/7136)
* k8s issue: [#65502](https://github.com/kubernetes/kubernetes/issues/65502)

### istio 场景
* pod 启动时，业务容器比 istio-proxy 先 ready。容器化过渡的应用，业务容器启动时需要调用其它服务，如果失败就退出，没有重试逻辑，而当 envoy 启动更慢时，业务容器调用其它服务失败，导致 pod 启动失败，如此循环。规避方法：改造业务，在启动逻辑中加重试逻辑。
* pod 销毁时，业务容器和 envoy 同时收到 SIGTERM，envoy 不再处理增量连接，但业务容器在 graceful shutdown 过程中可能需要调用另外的服务（比如通知其它清理进行清理操作)，这时候 envoy 就拒绝掉新的请求，导致调用失败。
* Job 运行完毕退出，但 istio sidecar 不会退出，导致 Job 永不退出 (Job 需要等所有 container 停止才算退出)

## 参考资料
