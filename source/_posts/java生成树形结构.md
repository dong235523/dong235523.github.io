---
title: java生成树形结构
date: 2024-08-02 10:25:38
tags: 
  - 工具类
categories: 后端技术
cover: false
---
收藏一个递归生成java树形结构的方法

```JAVA
package com.dp.generation.bean;

import java.util.List;

public class Xzqh {
    //部门代码
    private String dict_code;
    //父级部门代码
    private String dict_parent_code;
    //部门名称
    private String dict_label_full;
    private List<Xzqh> children;
    //部门级别
    private String dict_level;

    public String getDict_level() {
        return dict_level;
    }

    public void setDict_level(String dict_level) {
        this.dict_level = dict_level;
    }

    public List<Xzqh> getChildren() {
        return children;
    }

    public void setChildren(List<Xzqh> children) {
        this.children = children;
    }

    public String getDict_code() {
        return dict_code;
    }

    public void setDict_code(String dict_code) {
        this.dict_code = dict_code;
    }

    public String getDict_parent_code() {
        return dict_parent_code;
    }

    public void setDict_parent_code(String dict_parent_code) {
        this.dict_parent_code = dict_parent_code;
    }

    public String getDict_label_full() {
        return dict_label_full;
    }

    public void setDict_label_full(String dict_label_full) {
        this.dict_label_full = dict_label_full;
    }
}
```

```JAVA
	//List<Xzqh> childClassifyResp传入的组织机构list
	public static List<Xzqh> getClassifyTree(List<Xzqh> childClassifyResp) {
        // 返回的树形数据
        List<Xzqh> tree = new ArrayList<Xzqh>();
        // 第一次遍历
        for (Xzqh treeClassify : childClassifyResp) {
            // 找到根节点，这里我的根节点的判断条件是level为0
            if (treeClassify.getDict_level().equals("0")) {
                tree.add(findChild(treeClassify, childClassifyResp));
            }
        }
        return tree;
    }

    private static Xzqh findChild(Xzqh treeClassify, List<Xzqh> list) {
        // 定义list用于存储子节点
        List<Xzqh> children = new ArrayList<Xzqh>();
        for (Xzqh node : list) {
            // 找到根节点，这里我的根节点的pid为0
            if (node.getDict_parent_code().equals(treeClassify.getDict_code())) {
                // 调用递归
                children.add(findChild(node, list));
            }
        }
        treeClassify.setChildren(children);
        return treeClassify;
    }
```