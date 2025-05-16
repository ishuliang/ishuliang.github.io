---
title: lombok链式编程
description: "@Accessors(chain = true)注解"
date: 2024-07-27 09:40:03
image: 20240727.jpg
slug: /20240727
---

# lombok链式编程；可以一直点set方法

```java
new PatientPacsApplyPO()
                .setPatientIdent(patientIdent)
                .setHospitalNumber(hospitalNumber)
                .setGender(gender)
                .setPatientName(patientName);
```

要实现上述代码可以添加注解`@Accessors(chain = true)`
