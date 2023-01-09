---
title: java生成tree结构数据工具类
date: 2021-05-19 14:44:49
tags: 工具类
categories: 后端技术
cover: false
---
```
public class ComboTree implements Serializable{
    private String id;//：节点ID，对加载远程数据很重要。
    private String text;//：显示节点文本。
    private String parentid;//：节点ID，对加载远程数据很重要。
    private String state;//：节点状态，’open’ 或 ‘closed’，默认：’open’。如果为’closed’的时候，将不自动展开该节点。
    private boolean checked;//：表示该节点是否被选中。
    private String attributes;//: 被添加到节点的自定义属性。
    private List<ComboTree> children;//: 一个节点数组声明了若干节点。
 
    public String getId() {
        return id;
    }
 
    public void setId(String id) {
        this.id = id;
    }
 
    public String getParentid() {
        return parentid;
    }
 
    public void setParentid(String parentid) {
        this.parentid = parentid;
    }
 
    public String getText() {
        return text;
    }
 
    public void setText(String text) {
        this.text = text;
    }
 
    public String getState() {
        return state;
    }
 
    public void setState(String state) {
        this.state = state;
    }
 
    public boolean isChecked() {
        return checked;
    }
 
    public void setChecked(boolean checked) {
        this.checked = checked;
    }
 
    public String getAttributes() {
        return attributes;
    }
 
    public void setAttributes(String attributes) {
        this.attributes = attributes;
    }
 
    public List<ComboTree> getChildren() {
        return children;
    }
 
    public void setChildren(List<ComboTree> children) {
        this.children = children;
    }
 
}
```

```
public class TreeUtils {
 
    private List<ComboTree> menuList = new ArrayList<ComboTree>();
 
    public TreeUtils(List<ComboTree> menuList) {
        this.menuList = menuList;
    }
 
    //建立树形结构
    public List<ComboTree> builTree() {
        List<ComboTree> treeMenus = new ArrayList<ComboTree>();
        for (ComboTree menuNode : getRootNode()) {
            menuNode = buildChilTree(menuNode);
            treeMenus.add(menuNode);
        }
        return treeMenus;
    }
 
    //递归，建立子树形结构
    private ComboTree buildChilTree(ComboTree pNode) {
        List<ComboTree> chilMenus = new ArrayList<ComboTree>();
        for (ComboTree menuNode : menuList) {
            if (menuNode.getParentid().equals(pNode.getId())) {
                chilMenus.add(buildChilTree(menuNode));
            }
        }
        pNode.setChildren(chilMenus);
        return pNode;
    }
 
    //获取根节点
    private List<ComboTree> getRootNode() {
        List<ComboTree> rootMenuLists = new ArrayList<ComboTree>();
        for (ComboTree menuNode : menuList) {
        	//800000000000:顶层父节点，可以改成动态传入
            if (menuNode.getParentid().equals("800000000000")) {
                rootMenuLists.add(menuNode);
            }
        }
        return rootMenuLists;
    }
}
```