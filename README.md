# el-input-tree
基于el-input和el-tree的插件
在使用的界面导入
import ElInputTree from 'el-input-tree'
在template中使用
<el-input-tree class="org_tree" v-model="formObj.xx" ref="orgTree" placeholder="项目" :only-leaf-checked="true"/>