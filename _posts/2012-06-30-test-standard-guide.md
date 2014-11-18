---
layout: post
title: "测试标准（Beta 1.0）"
category: Technology
tags: Rails Test
---

总结了一下公司现在的测试实施情况，仅供参考：）

### 测试优先级
Model > Controller > Integration

### Model 测试
*  标准的 ActiveRecord 关联、validations不用测试，简单的 scope 也不需要测
*  尽可能提高 unit test的覆盖率
*  virtual attributes可考虑用通用的做法去测

### Controller 测试
*  符合 scaffold 标准的 action 可以不用测，例如：`def show;  User.find(params[:id]); end`
*  尽可能用真实数据模拟，让其自动执行，使用太多的stub会导致对测试缺乏安全感，宁可重复测试
*  执行完除了检查 http 状态外还需要检查结果，例如跳转后的页面或者相关属性的值等

### Integration 测试
*  只测关键功能（用capybara），而且留待最后再测
