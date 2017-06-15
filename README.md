# iview-select
iview UI库里面select组件的坑, 

##### on-change : 选择发生改变时触发的事件是
##### v-ref : 对应的值会挂载在全局vue（this里面可以访问到）上去，例如下面可以通过this.$refs.project对象里面找到当前选中项的一些数据


### api.js
``` const axios = require('axios');
    export const getProjectList = (userId) => {
      let data;
      axios.post(url,{userId: userId},config)
            .then(function(response){
            //因为iview里面的select模版需要value和label两个字段来渲染，因此我们可以
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
``` <template>
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
        //因为projectList对应的数据通常都是后台返回给我们的数据，所以我们在初始化的时候是不知道，每一项的projectId是多少的，因此我们可以判断取第一个项目
      },
      methods: {
        changeProject () {
          
        }
      }
    }
  </script> ```
