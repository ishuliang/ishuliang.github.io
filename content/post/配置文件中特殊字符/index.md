---
title: 配置文件中的特殊字符
description: yaml文件的特殊字符处理方式
date: 2025-05-14T14:40:41+08:00
image: 20240514.jpg
categories: "work"
---

# yaml文件中key包含特殊字符

在开发一个单位配置时，我想把他放到nacos上，每次读取配置文件，因为在yaml配置的key中包含特殊字符，比如：`+`、`(`、`)`等等，所以在springboot项目读取到map集合里面时所有的的这些特殊字符莫名消失了，因此导致系统出错了。

解决办法

在写key时使用`"[]"`将key值包起来

```yaml
# 血气分析单位表：
unit:
  map:
    pH: ""
    pCO2: "mmHg"
    pO2: "mmHg"
    "[K+]": "mmol/L"
    "[Na+]": "mmol/L"
    "[Ca++]": "mmol/L"
    "[Cl-]": "mmol/L"
    Glu: "mmol/L"
    Lac: "mmol/L"
    tHb: "g/dL"
    sO2: "%"
    T: "Cel"
    FIO2: "%"
    "[pH(T)]": ""
    "[pCO2(T)]": "mmHg"
    "[HCO3-]": "mmol/L"
    ABE: "mmol/L"
    SBE: "mmol/L"
    "[pO2(T)]": "mmHg"
    "[p50(act)]": "mmHg"
    AaDpO2: "mmHg"
    tO2: "mmol/L"
    RI: "%"
```

```java
@Data
@Component
@ConfigurationProperties(prefix = "unit")
public class UnitConfig implements InitializingBean {
    private Map<String, String> map;

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println(map);
    }
}
```

