## key的作用及原理

```js
 <div class="root">
      <!-- 遍历数组 -->
      <h2>person list</h2>
      <button @click="add">add a lao liu</button>
      <ul>
        <!-- <li v-for="person in personArr" :key="person.id">{{person.name}}-{{person.age}}</li> -->
        <li v-for="(person,index) in personArr" :key="index">
          {{person.name}}-{{person.age}}
        </li>
      </ul>
    </div>
    <script>
      const vm = new Vue({
        el: ".root",
        data: {
          personArr: [
            {
              id: "1",
              name: "张三",
              age: 132,
            },
            {
              id: "2",
              name: "李四",
              age: 132,
            },
            {
              id: "3",
              name: "王五",
              age: 132,
            },
          ],

        },
        methods: {
            add(){
                const p={id:'4',name:'lao liu',age:49}
                this.personArr.unshift(p)
            }
        },
      });
    </script>
```

当key是以index作为索引时，且在未加入老刘的信息后，其位置为

|      |      |
| ---- | ---- |
| 0    | 张三 |
| 1    | 李四 |
| 2    | 王五 |

然后我们在其后面添加一个`input`，且在原有数据的后面表格中添加他们的信息

| 0    | 老刘 |      |
| ---- | ---- | ---- |
| 1    | 张三 | 张三 |
| 2    | 李四 | 李四 |
| 3    | 王五 | 王五 |

由于`Vue`在渲染时会采用**虚拟DOM对比法**，在老刘加入表格时，会对原有内容进行比较，当存在相同内容时，便会直接保留，只渲染新的内容。

由于是采用index作为key,所以便会通过index的顺序进行比较

`0`时,原本为张三,现在为老刘,所以会重新渲染,但是张三的`input`仅仅只有真实DOM的内容不同,而虚拟DOM内容相同,所以老刘会直接继承张三的`input`,以此类推,所以王五会有一个原本属于老刘的`input`

### 使用唯一标识`id`作为key

因为`id`是每条信息的唯一标识,不会随着数据顺序的变化而改变,所以即便`老刘`的数据新增到第一列,也并不会将其`id`变为**0**.所以通过谁上面的 **虚拟DOM对比法** ,便是我们所预期的那样

### 原理

1. `虚拟DOM`中key的作用:

   key是虚拟DOM对象的标识,当数据发生变化时,`vue`会根据新数据生产新的虚拟DOM,随后`vue`会进行新的虚拟DOM与旧的虚拟DOM进行差异化比较

2. 对比规则

   - 当找到了与新的虚拟DOM相同的key时:
     1. 如果里面的内容没有改变,则会直接使用之间的真实DOM
     2. 如果里面的内容发生了改变,便会直接生成一个新的真实DOM,并替换掉原来的真实DOM
   - 未找到与新的虚拟DOM相同的key时:
     创建新的真实DOM,随后渲染到页面中