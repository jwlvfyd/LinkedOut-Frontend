<template>
  <el-dialog
    custom-class="job-dialog"
    v-model="visible" 
    :center="true" 
    :width="dialogWidth('582px')"
    :before-close="closeDialog"
    title="选择求职意向"
  >
    <el-cascader-panel
      v-model="selectedJobs"
      ref="jobCascader"
      style="height:250px;" 
      :options="jobOptions"
      :props="{ multiple: true, emitPath: false, checkStrictly: true }"
      @change="jobSelected"
    />
    <template #footer>
      <el-row justify="end">
        <el-button @click="$emit('close')">取消</el-button>
        <el-button type="primary" @click="submit">确认</el-button>
      </el-row>
    </template>
  </el-dialog>
</template>

<script>
import _ from "lodash";
import {updateUserInfo} from '@/apis/users.js';
import { dialogWidth } from '@/utils/utils.js';

export default {
  props: {
    visible: {
      type: Boolean,
      required: true
    }
  },
  created() {
    this.jobOptions = require('@/assets/job.json');
  },
  data() {
    return {
      jobOptions: {},
      selectedJobs: []
    }
  },
  methods: {
    dialogWidth: dialogWidth,
    closeDialog: function(done) {
      this.$emit('close');
      done();
    },
    jobSelected: function() {
      const disable = this.selectedJobs.length >= 3;
      this.setCanSelect(this.jobOptions, disable);
    },
    setCanSelect(items, disable) {
      items.forEach((item, index) => {
        if (_.get(item, "children", false)) { // 向子节点递归
          this.setCanSelect(item.children, disable);
        }
        else {
          items[index].disabled = this.selectedJobs.indexOf(item.value) > -1 ? false : disable;
        }
      });
      return items;
    },
    submit: async function() {
      const params = { 
        unifiedId: localStorage.getItem("unifiedId"),
        prePosition: this.selectedJobs.join()
      };
      const resp = await updateUserInfo(params);
      if (resp.status == 200 && resp.data.code == 'success') this.$message.success('保存成功!');
      else this.$message.error('保存失败!');
      this.$emit('close');
    }
  }
}
</script>

<style>
.el-cascader-menu__wrap.el-scrollbar__wrap {
  height: 260px;
}
.job-dialog {
  --el-dialog-padding-primary: 20px;
}
.job-dialog .el-dialog__body {
  padding: 10px 20px;
}
</style>
