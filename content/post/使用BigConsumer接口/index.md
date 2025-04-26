---
title: "记一次使用BiConsumer记录"
description: "使用BigConsumer优化代码"
date: 2025-02-20T15:17:11+08:00
image: index.JPG
math: false
hidden: false
comments: false
draft: false
---

# 记一次使用BiConsumer记录

## 遇到的问题

在对接HL7中过程中遇到了一段代码，非常繁琐，想找找优化代码的方案，询问了ChatGPT，回答了这个接口。

```java
switch (type) {
    case "IMP":
        pacsPO.setImpression(this.getHl7Value(data));
        break;
    case "REC":
        pacsPO.setAdvice(this.getHl7Value(data));
        break;
    case "GDT":
        pacsPO.setSee(this.getHl7Value(data));
        break;
    case "RRP":
        pacsPO.setReportUrl(this.getHl7Value(data));
        break;
    case "ZMF":
        pacsPO.setMasterReportFile(this.getHl7Value(data));
        break;
    case "ZRF":
        pacsPO.setRelationReportFile(this.getHl7Value(data));
        break;
    case "AFD":
        pacsPO.setObserValue(this.getHl7Value(data));
        break;
    case "ANT":
        break;
    case "I9":
        pacsPO.setDiagName(this.getHl7Value(data));
        break;
    case "CMR":
        continue;
    default:
        continue;
}
```

大体特征是每个类型对应一个set方法。

## 优化方案

先初始化这个map，放进去一个方法

```java
/**
 * typeHandlerMap用于存放对应类型所应该字段的赋值
 **/
private static Map<String, BiConsumer<PatientPacsResultPO, Type>> typeHandlerMap = new HashMap<>();

/**
 * 初始化typeHandlerMap
 * 2025/2/20 9:52
 **/
static {
    typeHandlerMap.put("IMP", (po, data) -> po.setImpression(getHl7Value(data)));
    typeHandlerMap.put("GDT", (po, data) -> po.setSee(getHl7Value(data)));
    typeHandlerMap.put("REC", (po, data) -> po.setAdvice(getHl7Value(data)));
    typeHandlerMap.put("RRP", (po, data) -> po.setReportUrl(getHl7Value(data)));
    typeHandlerMap.put("ZMF", (po, data) -> po.setMasterReportFile(getHl7Value(data)));
    typeHandlerMap.put("ZRF", (po, data) -> po.setRelationReportFile(getHl7Value(data)));
    typeHandlerMap.put("AFD", (po, data) -> po.setObserValue(getHl7Value(data)));
    typeHandlerMap.put("I9", (po, data) -> po.setDiagName(getHl7Value(data)));
}
```

然后方法中调用

```java
typeHandlerMap.getOrDefault(type, (po, d) -> {
                    }).accept(pacsPO, data);
```