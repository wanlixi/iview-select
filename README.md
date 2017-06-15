# iview-select
iview UI库里面select组件的坑, 

##### on-change : 选择发生改变时触发的事件是
##### v-ref : 对应的值(我们设置的是project)会挂载在全局vue（this里面可以访问到）上去，例如下面可以通过this.$refs.project对象里面找到当前选中项的一些数据

[config配置](http://www.cnblogs.com/Upton/p/6180512.html)

### api.js
``` 
    const axios = require('axios');
    export const getProjectList = (userId) => {
      let data;
      axios.post(url,{userId: userId},config)
            .then(function(response){
            // 因为iview里面的select模版需要value和label两个字段来渲染，因此我们可以
              data = response.map( item => {
                item.value = item.projectId;
                item.label = item.projectName;
              })
              return data;
            })
            .catch(function(err){
              //
            })
    }
```
### index.vue
``` 
  <template>
    <i-select v-ref:project :model.sync="selectedProjectId" @on-change="changeProject">
      <i-option v-for="item in projectList" :value="item.value">{{item.label}}</i-option>
    </i-select>
  </template>
  <script type="text/babel">
    // 这里引入iview 里面的select组件
    import { iSelect , iOption } from "iview"
    export default {
    components: { iSelect , iOption },
      data () {
        projectList: getProjectList,
        selectedProjectId: null,
        selectedProjectName: '',
        selectedProjectLogo: '',
      },
      mounted () {
        // 因为projectList对应的数据通常都是后台返回给我们的数据，所以我们在初始化的时候是不知道，每一项的projectId是多少的，因此我们可以判断取第一个项目
        this.projectList.length > 0 && this.selectedProjectId = this.projectList[0].projectId;
        console.log(456)
      },
      methods: {
        changeProject () {
          // 注意，坑1!!! 下面打印的123会在页面初始化的时候打印一次,而且会打印在456之后，我们通常误认为changeProject绑定的on-change事件，应该是只有在select的选中值发生改变时，才能触发此事件
          console.log(123);
          this.selectedProjectId = this.$refs.project.model;
          // 注意，坑2 ，虽然我们在控制台里面可以找到我们当前选中值projectName，但是却不能直接赋值给this.selectedProjectName，也就是说this.$refs.project.selectedSingle取不到值，如果用户一进来没有切换过项目，直接提交数据传过的数据就是这样的{ projectId: （正常有值）, projectName: undefind,所以我们不可以这样给selectedProjectName赋值
          this.selectedProjectName = this.$refs.project.selectedSingle;
          // 而是
          this.selectedProjectName = this.projectList.filter( item => {
            return item.value == this.selectedProjectId
          })
        }
      }
    }
  </script> 
  ```
  \<html\>
