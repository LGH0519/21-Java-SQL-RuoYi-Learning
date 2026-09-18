这几个概念都和“把一个状态交给页面使用”有关，但它们处于不同层面：

| 写法                         | 本质              | 主要作用           |
| -------------------------- | --------------- | -------------- |
| `v-loading="queryLoading"` | Element Plus 指令 | 给一块区域加加载遮罩     |
| `:loading="saving"`        | 给组件传递 prop      | 让某个组件进入加载状态    |
| `computed()`               | Vue 计算属性        | 根据现有响应式数据计算新状态 |
| `props`                    | 组件输入参数          | 父组件把数据传给子组件    |

---

# 一、`v-loading` 和 `:loading`

虽然它们都包含 `loading`，但不是一回事。

## 1. `v-loading`

例如：

```
<div
  class="app-container"
  v-loading="queryLoading"
>
  页面内容
</div>
```

`v-loading` 是 Element Plus 提供的自定义指令。绑定值为 `true` 时，它会在当前元素上覆盖一层加载遮罩。[Element Plus Loading 文档](https://element-plus.org/zh-CN/component/loading)

假设：

```
const queryLoading = ref(false)
```

开始查询：

```
queryLoading.value = true
```

页面效果大致是：

```
┌────────────────────────┐
│                        │
│       转圈加载中        │
│       页面被遮住        │
│                        │
└────────────────────────┘
```

查询结束：

```
queryLoading.value = false
```

遮罩消失。

### `v-loading` 的作用范围

遮罩范围由它放在哪个元素上决定。

放在整个页面容器：

```
<div v-loading="queryLoading">
```

就是整个容器加载。

放在表格：

```
<el-table
  v-loading="listLoading"
  :data="registrationList"
>
```

就是只遮住表格。

放在某张卡片：

```
<el-card v-loading="detailLoading">
```

就是只遮住这张卡片。

### 什么时候使用？

适合：

- 第一次进入页面查询数据；
- 查询整个列表；
- 切换详情记录；
- 一块较大区域的数据还没返回；
- 希望用户暂时不要操作整块区域。

在你的个人报名页面：

```
<div v-loading="queryLoading">
```

是合理的，因为第一次打开页面时，需要先从后端查询当前报名信息。查询结束前，整张表单不应该让用户操作。

---

## 2. `:loading`

例如：

```
<el-button
  :loading="saving"
>
  保存
</el-button>
```

这里的 `loading` 是 `el-button` 组件提供的属性，也就是一个 prop。

Element Plus 的 Button 文档中定义了：

```
loading：是否处于加载状态
类型：boolean
默认值：false
```

[Element Plus Button 文档](https://element-plus.org/zh-CN/component/button)

当：

```
saving.value = true
```

按钮大致变成：

```
[ 转圈 保存 ]
```

影响范围只有这个按钮，不会遮住整个页面。

### 什么时候使用？

适合表示某个具体操作正在执行：

```
<el-button :loading="saving">
  保存
</el-button>

<el-button :loading="submitting">
  提交
</el-button>

<el-button :loading="withdrawing">
  撤回
</el-button>
```

这样可以准确告诉用户：

- 现在正在保存；
- 或正在提交；
- 或正在撤回。

而不是笼统地把整个页面遮住。

---

## 3. 两者对比

```
<div v-loading="queryLoading">
  <el-button :loading="saving">
    保存
  </el-button>
</div>
```

分别表示：

```
queryLoading = true
→ 整个div出现遮罩

saving = true
→ 只有保存按钮转圈
```

|场景|建议|
|---|---|
|页面首次查询|`v-loading`|
|表格重新查询|表格上使用 `v-loading`|
|查询详情|详情区域使用 `v-loading`|
|点击保存|按钮使用 `:loading`|
|点击提交|按钮使用 `:loading`|
|点击撤回|按钮使用 `:loading`|
|上传单个文件|上传按钮使用自己的 loading|
|整块附件列表刷新|附件区域使用 `v-loading`|

## 4. 为什么一个是 `v-`，另一个是 `:`？

### `v-loading`

以 `v-` 开头，说明它是“指令”：

```
v-loading
v-if
v-for
v-model
v-show
```

指令会要求 Vue 或插件对元素执行某种行为。

### `:loading`

开头的 `:` 是：

```
v-bind:loading
```

的简写。

也就是把 JavaScript 数据绑定给组件的 `loading` 属性：

```
:loading="saving"
```

等价于：

```
v-bind:loading="saving"
```

所以可以记成：

```
v-loading：在这个元素上执行加载遮罩行为
:loading：把loading数据交给这个组件
```

---

# 二、`computed()` 解析

## 1. 当前项目中的例子

```
const isEditable = computed(() => {
  return ["0", "3", "4"].includes(
    form.value.status
  )
})
```

它的含义是：

```
根据 form.status
计算当前报名是否允许编辑
```

状态规则是：

```
0 草稿         → 可以编辑
1 待审核       → 不可以编辑
2 审核通过     → 不可以编辑
3 审核不通过   → 可以编辑
4 已撤回       → 可以编辑
```

所以它相当于：

```
const isEditable = computed(() => {
  const currentStatus = form.value.status

  if (
    currentStatus === "0" ||
    currentStatus === "3" ||
    currentStatus === "4"
  ) {
    return true
  }

  return false
})
```

原来的写法只是更简洁：

```
["0", "3", "4"].includes(form.value.status)
```

`includes()` 表示检查数组里是否存在这个值。

---

## 2. `computed()` 接收了什么？

```
computed(() => {
  return 计算结果
})
```

传给 `computed()` 的是一个函数：

```
() => {
  return ...
}
```

这个函数也叫“getter”，负责计算结果。

完整拆开可以想象成：

```
function calculateEditable() {
  return ["0", "3", "4"].includes(
    form.value.status
  )
}

const isEditable = computed(
  calculateEditable
)
```

通常直接使用箭头函数，因此写成：

```
const isEditable = computed(() => {
  return ...
})
```

---

## 3. 它会自动跟踪依赖

计算过程中使用了：

```
form.value.status
```

Vue 会知道：

```
isEditable依赖form.status
```

当状态变化：

```
form.value.status = "1"
```

Vue 会重新计算：

```
isEditable = false
```

当撤回成功：

```
form.value.status = "4"
```

Vue 又会重新计算：

```
isEditable = true
```

不需要手动写：

```
isEditable.value = true
```

这就是计算属性的主要作用。

---

## 4. `computed()` 返回什么？

它返回一个“计算属性 ref”，不是普通布尔值。

所以在 JavaScript 中要写：

```
isEditable.value
```

例如：

```
if (!isEditable.value) {
  return
}
```

但是在模板中，Vue 会自动拆开 `.value`：

```
<el-input :disabled="!isEditable" />
```

模板中不要写成：

```
<el-input :disabled="!isEditable.value" />
```

可以这样记：

```
<script 中的 ref/computed → 通常需要 .value
template 中的 ref/computed → Vue 自动处理 .value
```

---

## 5. 为什么不用普通变量？

如果写成：

```
const isEditable =
  ["0", "3", "4"].includes(
    form.value.status
  )
```

页面初始化时只计算一次。

假设初始状态：

```
form.value.status = "0"
```

那么：

```
isEditable = true
```

后端查询完成后，状态变成：

```
form.value.status = "1"
```

普通变量 `isEditable` 仍然可能保持原来的 `true`。

而使用：

```
const isEditable = computed(...)
```

它会随着 `form.status` 自动更新。

---

## 6. `computed()` 和普通方法有什么区别？

也可以写成方法：

```
function isEditable() {
  return ["0", "3", "4"].includes(
    form.value.status
  )
}
```

模板中需要调用：

```
:disabled="!isEditable()"
```

结果也能实现。

主要区别是：计算属性会根据响应式依赖缓存结果；依赖没有变化时，不需要反复计算。普通方法在组件重新渲染时会再次执行。[Vue 计算属性文档](https://cn.vuejs.org/guide/essentials/computed)

当前判断很简单，性能差别几乎可以忽略。但语义上：

```
isEditable
```

表达“这是一个状态”。

```
isEditable()
```

表达“现在执行一次判断”。

所以这里使用 `computed()` 更自然。

---

## 7. 什么时候使用 `computed()`？

适合“从已有数据推导另一个数据”：

```
const isEditable = computed(() => {
  return ["0", "3", "4"].includes(
    form.value.status
  )
})
```

```
const canWithdraw = computed(() => {
  return form.value.status === "1"
})
```

```
const isResubmission = computed(() => {
  return ["3", "4"].includes(
    form.value.status
  )
})
```

以后还可能有：

```
const uploadedFileCount = computed(() => {
  return attachmentList.value.length
})
```

```
const canSubmit = computed(() => {
  return isEditable.value &&
         form.value.realName &&
         form.value.phoneNumber &&
         form.value.applyPosition
})
```

### 不适合放什么？

不要在 `computed()` 中：

- 请求后端；
- 修改数据库；
- 弹出提示框；
- 修改其他响应式变量；
- 执行上传；
- 执行提交。

不建议：

```
const result = computed(() => {
  saveDraft() // 错误思路
})
```

`computed()` 应尽量只负责“读取数据并计算结果”，不要产生副作用。

---

# 三、`props` 解析

## 1. 先理解组件

例如：

```
<el-button>
  保存
</el-button>
```

`el-button` 是 Element Plus 写好的一个子组件。

你当前的 `index.vue` 使用了它：

```
index.vue（父组件）
    ↓ 传递参数
el-button（子组件）
```

父组件想告诉按钮：

- 按钮类型是什么；
- 是否正在加载；
- 是否禁用；
- 使用什么图标。

于是写：

```
<el-button
  type="primary"
  :loading="saving"
  :disabled="!isEditable"
  icon="DocumentChecked"
>
  保存
</el-button>
```

这里的：

```
type
loading
disabled
icon
```

都是 `el-button` 接收的 props。

可以把 props 理解成：

```
组件的输入参数
```

类似 Java 方法参数：

```
saveRegistration(userId, year)
```

组件也需要参数：

```
<el-button
  type="primary"
  :loading="saving"
>
```

---

## 2. 静态 prop 和动态 prop

### 静态值

```
<el-button type="primary">
```

这里直接传字符串：

```
type = "primary"
```

因为值永远是固定的，不需要冒号。

### 动态值

```
<el-button :loading="saving">
```

这里传的是 JavaScript 变量：

```
saving
```

所以需要冒号。

### 数字也建议动态绑定

```
<el-input :rows="6" />
```

传入数字：

```
6
```

如果写：

```
<el-input rows="6" />
```

原始形式是字符串 `"6"`。组件可能会转换，但明确传数字更规范。

---

## 3. 自己写组件时怎样声明 props？

假设创建一个状态卡片组件：

```
RegistrationStatus.vue
```

子组件：

```
<template>
  <div>
    当前状态：{{ status }}
  </div>
</template>

<script setup>
const props = defineProps({
  status: {
    type: String,
    required: true
  }
})
</script>
```

这里声明：

```
这个组件需要接收一个名为status的字符串
```

父组件使用：

```
<RegistrationStatus
  :status="form.status"
/>
```

数据方向：

```
父组件 form.status
        ↓ props
子组件 status
```

Vue 的 `defineProps()` 是 `<script setup>` 中使用的编译宏，不需要手动导入。[Vue Props 文档](https://cn.vuejs.org/guide/components/props)

---

## 4. props 可以设置哪些规则？

```
const props = defineProps({
  status: {
    type: String,
    required: true
  },

  title: {
    type: String,
    default: "报名状态"
  },

  editable: {
    type: Boolean,
    default: false
  }
})
```

含义分别是：

```
status：
必须传，类型必须是String

title：
可以不传，不传时默认“报名状态”

editable：
可以不传，不传时默认false
```

父组件：

```
<RegistrationStatus
  :status="form.status"
  title="当前报名状态"
  :editable="isEditable"
/>
```

---

## 5. 子组件不能直接修改 props

props 是单向数据流：

```
父组件 → 子组件
```

子组件不应该直接写：

```
props.status = "1"
```

因为这个数据属于父组件。

如果子组件希望父组件修改，应该发出事件：

```
<script setup>
const emit = defineEmits([
  "status-change"
])

function requestChange() {
  emit("status-change", "1")
}
</script>
```

父组件监听：

```
<RegistrationStatus
  :status="form.status"
  @status-change="form.status = $event"
/>
```

可以类比为：

```
props：父亲把数据交给孩子看
emit：孩子通知父亲发生了什么
```

---

# 四、当前页面中哪些属于 props？

例如：

```
<el-button
  type="success"
  icon="UploadFilled"
  :loading="submitting"
  :disabled="!isEditable"
>
```

这里：

```
type       → prop
icon       → prop
loading    → prop
disabled   → prop
```

```
<el-input
  v-model="form.realName"
  :disabled="!isEditable"
  maxlength="11"
  placeholder="请输入真实姓名"
/>
```

这里：

```
disabled     → prop
maxlength    → prop
placeholder  → prop
```

```
<el-col
  :xs="24"
  :md="12"
>
```

这里：

```
xs → prop
md → prop
```

而下面这些不是 props：

```
v-loading="queryLoading"
```

这是指令。

```
@click="handleSaveDraft"
```

这是事件监听。

```
v-if="registrationDetail"
```

这是 Vue 指令。

```
class="registration-card"
```

这是 HTML/CSS 类属性。

---

# 五、用一句话区分

```
v-loading：
让某块区域出现遮罩。

:loading：
把“是否加载中”传给某个组件。

computed：
根据响应式数据自动计算另一个状态。

props：
父组件传给子组件的输入参数。
```

在当前报名页面中的关系是：

```
form.status
    ↓ computed
isEditable、canWithdraw、isResubmission
    ↓ props
el-input的disabled
el-button的disabled

queryLoading
    ↓ v-loading
整个报名页面显示加载遮罩

saving
    ↓ :loading
保存按钮显示转圈
```