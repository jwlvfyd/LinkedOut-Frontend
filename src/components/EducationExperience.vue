<template>
  <el-container direction="horizontal" style="margin-bottom:0px">
    <user-icon :url="picUrl" :size="50"/>
    <el-container direction="vertical" style="margin:0px 0px 20px 10px">
      <el-row>
        <el-col :span="20">
          <div style="font-size:16px; margin-bottom:5px"><b>{{ college }}</b></div>
        </el-col>
        <el-col :span="4">
          <el-button @click="dialogVisible=true" type="mini" v-if="modifiable">修改</el-button>
        </el-col>
      </el-row>
      <div style="font-size:15px; margin-bottom:5px">{{major}}·{{degree}}</div>
      <div style="font-size:12px; color:rgb(122,122,122);">{{startTime}}-{{endTime}}</div>
    </el-container>
  </el-container>

  
  <el-dialog v-model="dialogVisible" title="修改教育经历">
    <el-form ref="form" :model="form" label-width="200px" :rules="rules">
      <el-form-item label="学校" prop="college" required>
        <el-input v-model="form.college" placeholder="例如:清华大学"></el-input>
      </el-form-item>
             
      <el-form-item label="专业" prop="major" required>
        <el-input v-model="form.major" placeholder="例如:软件工程"></el-input>
      </el-form-item>

      <el-form-item label="学位" prop="degree" required>
        <el-select v-model="form.degree" placeholder="请选择学位">
          <el-option label="专科" value="专科"></el-option>
          <el-option label="本科" value="本科"></el-option>
          <el-option label="硕士" value="硕士"></el-option>
          <el-option label="博士" value="博士"></el-option>
        </el-select>
      </el-form-item>
              
      <el-form-item label="开始时间" prop="startTime" required>
        <el-date-picker
        v-model="form.startTime"
        type="month"
        placeholder="请选择开始时间"
        >
        </el-date-picker>
      </el-form-item>
              
      <el-form-item label="结束时间" prop="endTime" required>
        <el-date-picker
        v-model="form.endTime"
        type="month"
        placeholder="请选择结束时间"
        >
        </el-date-picker>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submitForm('form')">提交</el-button>
        <el-button @click="resetForm('form')">重置</el-button>
      </el-form-item>
    </el-form>
  </el-dialog>
</template>

<script>
import UserIcon from '@/components/UserIcon'

export default {
   data(){
     return{
       dialogVisible: false,
       form:{
         educationExperienceId: 0,
         college: '同济大学',
         major: '软件工程',
         degree: '本科',
         startTime: '2019年9月',
         endTime: '2023年7月'
       },
       rules: {
        college: [
          {
            required: true,
            message: '请输入学校',
            trigger: 'change',
          },
        ],
        major: [
          {
            required: true,
            message: '请输入专业',
            trigger: 'change',
          },
        ],
        degree: [
          {
            required: true,
            message: '请选择学位',
            trigger: 'change',
          },
        ],
        startTime: [
          {
            type: 'date',
            required: true,
            message: '请选择开始时间',
            trigger: 'change',
          },
        ],
        endTime: [
          {
            type: 'date',
            required: true,
            message: '请选择结束时间',
            trigger: 'change',
          },
        ],
      },
     }
   },
   components:{
      UserIcon
   },
   created(){
      this.form.educationExperienceId=this.educationExperienceId
      this.form.college=this.college
      this.form.major=this.major
      this.form.degree=this.degree
      this.form.startTime=this.startTime
      this.form.endTime=this.endTime
    },
   props: {
    modifiable: {
      type: Boolean,
      default: false
    },
    educationExperienceId: { 
      type: Number,
      required: true
    },
    college: { 
      type: String,
      required: true
    },
    major: { 
      type: String,
      required: true
    },
    degree: {
      type: String,
      required: true
    },
    startTime: {
      type: String,
      required: true
    },
    endTime: {
      type: String,
      required: true
    },
    picUrl: {
      type: String,
      default: ''
    }
  },
  methods:{
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          alert('教育经历修改成功!')
          this.$emit('modify',this.form)
          this.dialogVisible=false
        } else {
          return false
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    },
  }
}
</script>

<style>
.el-divider {
  margin: 10px 0px;
}
</style>