# 最佳实践指南

## 关于commit元数据

原则上不建议在属性中存储能够通过commit元数据确定的数据，如能通过提交时间确定的时间。

## 内容的点赞/踩

标准库中的trait：

```bcs
trait likeable {
    iterset like: option<bool>
}
```

其中，属性like的值：`some(true)`为点赞，`some(false)`为点踩，`none`为还原初始值。

### 获取当前用户点赞/踩状态

使用query by operator。由于是iterset，得到的是唯一值，即一个`option<bool>`。再按照以上定义解释值即可。

### 如果不需要点踩

添加额外validator，拒绝`some(false)`值。

### 如果只统计点赞数

添加reducer，reduced value类型`uint`，为点赞数；reducer每遇到一个`some(true)`值增加1，忽略其他所有值。

### 如果统计点赞点踩总和

添加reducer，reduced value类型`int`，为点赞点踩总和数；reducer每遇到一个`some(true)`值增加1，每遇到一个`some(false)`值减少1，忽略`none`值。

### 如果分别统计点赞点踩值

添加reducer，reduced value类型`uint`，为点赞数；reducer每遇到一个`some(true)`值增加1，忽略其他所有值。

添加reducer，reduced value类型`uint`，为点踩数；reducer每遇到一个`some(false)`值增加1，忽略其他所有值。建议不要直接存储为有符号整数，而是存储为无符号整数，显示该值的时候再在前面加上负号。

## 内容的（类）投币

标准库中的trait：

```bcs
trait voteable {
    iterlist vote: uint
    defaultable mut vote-max: uint
}
```

其中，属性`vote`有一个reducer by operator，reduced value类型为`uint`，名为`votes`，reducer行为是将所有值加在一起；validator是检查当前提交操作者的`votes`加上当前值是否超过`vote-max`；

属性`vote-max`可添加额外validator改为`const`。

### 获取当前用户投币数状态

使用`votes`。

### 获取内容投币总数

添加reducer，reduced value类型`uint`，为投币总数；reducer行为是将所有值加在一起。

【将以上这些「添加reducer/validator」全部写入标准库】
