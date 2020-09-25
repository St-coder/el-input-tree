<template>
  <el-popover
    placement="bottom-start"
    width="200"
    trigger="click"
    transition="el-zoom-in-top"
    class="el-input-tree"
    popper-class="el-input-tree__popper"
    v-model="visible"
    @hide="handleHide"
  >
    <div class="el-input-tree__tree-container" ref="tree-container">
      <el-tree
        ref="tree"
        :data="data"
        :show-checkbox="showCheckbox"
        :check-strictly="checkStrictly"
        node-key="id"
        :filter-node-method="filterNode"
        :default-expanded-keys="defaultExpandedKeys"
        :default-checked-keys="defaultCheckedKeys"
        :props="defaultProps"
        @node-click="handleNodeClick"
        @check-change="handleCheckChange"
      ></el-tree>
    </div>
    <el-input
      slot="reference"
      v-model="name"
      :clearable="true"
      class="el-input-tree__input"
      :class="{'el-input-tree_arrow-down':!visible,'el-input-tree_arrow-up':visible}"
      :placeholder="placeholder"
      suffix-icon="el-icon-arrow-up"
      @change="handleChange"
      @input="handleInput"
      @click.native="handleClick"
    ></el-input>
  </el-popover>
</template>

<script>
export default {
  props: {
    value: [Object, String],
    defaultExpandedKeys: Array,
    defaultCheckedKeys: Array,
    showCheckbox: Boolean,
    checkStrictly: Boolean,
    /**
     * 是否包行半选状态的节点
     */
    hasHalfChecked: { type: Boolean, default: false },
    /**
     * 仅返回叶节点数据
     */
    onlyLeafChecked: { type: Boolean, default: false },
    placeholder: String,
    onlyValue: [String, Number],
    id: [String, Number],
    width: {
      default: 200
    },
    /***'
     * 对应orgKindId: ogn,dpt,pos,psm
     * 和typeSign： 3，5,7,9
     *
     * */
    filterLevel: String,
    selectLevel: String,
    /**
     * 针对不通过树传递不同的 url,但是要求server端返回的数据必须符合下列格式
     * {id:"", parentId:"", name:"", children:"", childrens:[]}
     */
    url: {
      type: String,

      default: "/soi/api/v1/organization/orgTreeByprojectIdsDA"
      // default: "/soi/api/v1/organization/orgShortTree"
    }
  },
  data() {
    return {
      name: "",
      data: [],
      selectNodes: [],
      visible: false,
      defaultProps: {
        isLeaf: data => !data.children,
        label: "name",
        children: "childrens",
        disabled: "disabled"
      }
    };
  },
  created() {
    // 文本框的默认值.
    this.name = this.value;
    this.axios.get(this.url).then(result => {
      this.data = result.data;
    });
  },
  methods: {
    handleHide() {},
    handleChange(val) {
      this.$refs.tree.filter(val);
      if(!val){
        this.$refs.tree.setCheckedNodes([]);
      }
    },
    handleClick() {},
    handleNodeClick() {},
    handleCheckChange() {
      let nodes = (this.selectNodes = this.$refs.tree.getCheckedNodes(
        this.onlyLeafChecked,
        this.hasHalfChecked
      ));
      this.name = nodes.map(item => item.name).join(",");
    },
    handleInput() {
      this.$refs.tree.filter(this.name);
    },
    filterNode(value, data) {
      if(!value){
        return true;
      }
      return data.name.indexOf(value) !== -1;
    },
    /**
     * hasHalfChecked
     * 是否包行半选状态的节点
     *
     * onlyLeafChecked
     * 仅返回叶节点数据
     */
    getCheckedNodes(onlyLeafChecked, hasHalfChecked) {
      return this.$refs.tree.getCheckedNodes(onlyLeafChecked, hasHalfChecked);
    }
  },
  watch: {
    visible(newVal) {
      if(!newVal)
      this.$emit("up", this.selectNodes);
    },
  }
};
</script>

<style lang="scss">
.el-input-tree__popper {
  height: 200px;
  overflow: auto;
  resize: both;
}

.el-input-tree_arrow-down {
  .el-input__icon {
    transform: rotateZ(180deg);
  }
}

.el-input-tree_arrow-up {
  .el-input__icon {
    transform: rotateZ(0deg);
  }
}

.el-input-tree__tree-container {
  overflow-y: auto !important;
}

.el-input-tree__input {
  outline: 0 !important;

  &:focus {
    outline: 0 !important;
  }

  span {
    outline: 0 !important;

    &:focus {
      outline: 0 !important;
    }
  }

  i {
    outline: 0;

    &:focus {
      outline: 0 !important;
    }
  }
}
</style>
