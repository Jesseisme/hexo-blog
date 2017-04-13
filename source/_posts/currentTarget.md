---
title: DOM属性currentTarget
tags: 
  - javascript
---
```
<html>
<head>
<script type="text/javascript">
function getEventTrigger(event)
  { 
  x=event.currentTarget; 
console.log(event)
console.log(event.currentTarget)
  }
</script>
</head>
<body >
<p id="p1" onmousedown="getEventTrigger(event)">
Click on this paragraph. An alert box will
show which element triggered the event.</p>
</body>
</html>
```

### 运行此代码会发现： **console.log(event) currentTarget:null**  而直接打印currentTarget则返回当前节点

#### 这是由于打印event的时候currentTarget被重置为null了，用debug查看的话就不会出现null这种情况