---
layout: home

title: luchen
titleTemplate: 基于 go-kit 封装的微服务框架

hero:
  name: luchen
  text: 基于 go-kit 封装的微服务框架
  tagline: 开箱即用，封装了工程实践中常用的组件和工具 
  actions:
    - theme: brand
      text: 快速开始
      link: /guide/getting-started
    - theme: alt
      text: GitHub
      link: https://github.com/fengjx/luchen
  image:
      src: /luchen-logo.svg
      alt: luchen

features:
  - icon: 🌐
    title: 多协议支持
    details: 上手简单，支持 HTTP、gRPC 多种传输协议。
  - icon: 🖥
    title: 易扩展
    details: 秉承 go-kit 的简单和设计理念，单体服务和微服务都有支持。
  - icon: 🚀
    title: 完善工具链
    details: 使用 proto 文件定义服务接口，生成接口和 crud 代码

---
<style>
:root {
  --vp-home-hero-name-color: transparent;
  --vp-home-hero-name-background: -webkit-linear-gradient(120deg, #bd34fe 30%, #41d1ff);

  --vp-home-hero-image-background-image: linear-gradient(-45deg, #bd34fe 50%, #47caff 50%);
  --vp-home-hero-image-filter: blur(44px);
}

@media (min-width: 640px) {
  :root {
    --vp-home-hero-image-filter: blur(56px);
  }
}

@media (min-width: 960px) {
  :root {
    --vp-home-hero-image-filter: blur(68px);
  }
}
</style>
