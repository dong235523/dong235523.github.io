---
title: SpringMVC controller用Map接收任意参数
date: 2018-08-17 14:54:05
tags: 解决方案
categories: 后端技术
cover: false
---
``` code
@RequestMapping(value="/test",method={RequestMethod.POST,RequestMethod.GET})
@ResponseBody
public String test(@RequestParam Map<String,String> map){

}
``` 
___
map前边要加上@RequestParam，参数才能自动进入到map中
