<template>
  <top-nav/>
  <el-row justify="center" style="margin-top:20px">
    <el-col :sm="15" :xs="24">
      <el-form ref="form" :model="form" :rules="rules" label-width="120px">
        <!-- 职位基础信息 -->
        <el-card>
          <template #header>
            <span><b>职位基础信息</b></span>
          </template>
            <el-form-item label="招聘职位名称" prop="title">
              <el-input style="width:60%" v-model="form.title" placeholder="Please input"></el-input>
            </el-form-item>
            <el-form-item label="选择职位分类" prop="classification">
              <el-cascader 
                v-model="form.classification" 
                :options="jobOptions" 
                :show-all-levels="false"
                :props="{ disabled: 'null', emitPath: false,}" 
              />
            </el-form-item>
            <el-form-item label="职位薪资描述" prop="salary">
              <el-input style="width:80%" v-model="form.salary" placeholder="例如: 20-40K·15薪"></el-input>
            </el-form-item>
            <el-form-item label="招聘职位限制" prop="limit">
              <el-input style="width:80%" v-model="form.limit" placeholder="例如: 上海 · 1-3年 · 本科"></el-input>
            </el-form-item>
            <el-form-item label="工作地址" prop="location">
              <!-- <el-input style="width:80%" v-model="form.location" placeholder="例如: 上海市嘉定区曹安公路4800号同济大学"></el-input> -->
              <el-autocomplete
                style="width:80%"
                v-model="form.location"
                :fetch-suggestions="querySearch"
                placeholder="例如: 上海市嘉定区曹安公路4800号同济大学"
                @select="handleSelect"
              />
            </el-form-item>
        </el-card>

        <!-- 职位详情 -->
        <el-card style="margin-top: 20px">
          <template #header>
            <span><b>职位详情</b></span>
            <span style="margin-left:10px; font-size:10px; color: rgb(122 122 122);">
              (支持markdown语法编辑)
            </span>
          </template>
            <div id="vditor"/>
          <el-row justify="center" style="margin-top:20px">
            <el-button type="primary" @click="submit">发布职位</el-button>
          </el-row>
        </el-card>
      </el-form>
    </el-col>
  </el-row>
  <page-footer/>
</template>

<script>
import TopNav from '@/components/TopNav';
import AMapLoader from '@amap/amap-jsapi-loader';
import PageFooter from '@/components/PageFooter';
import Vditor from 'vditor';
import '@/assets/vditor.css';
import { addPosition } from '@/apis/recruit.js';

export default {
  components: {
    TopNav,
    PageFooter
  },
  created() {
    AMapLoader.load({ // 地图加载器
      key: '7b0368f9798bd0bfe3d901a47f90f8f6',
      plugins: ['AMap.PlaceSearch']
    }).then((Amap) => {
      this.placeSearch = new Amap.PlaceSearch({ extensions: 'all' });
    });
    this.jobOptions = require('@/assets/job.json');
  },
  mounted() {
    this.vditor = new Vditor('vditor', {
      mode: 'wysiwyg',
      placeholder: '请输入职位详情...',
      input: value => this.form.details = value,
      cache: { enable: false },
    });
  },
  data() {
    return {
      placeSearch: null,
      jobOptions: null,
      vditor: null,
      form: {
        title: '',
        salary: '',
        limit: '',
        location: '',
        classification: '',
        details: ''
      },
      rules: {
        title: [
          {
            required: true,
            message: '请输入招聘职位名称',
          },
          {
            min: 2,
            max: 25,
            message: '2-25个字符'
          }
        ],
        salary: {
          required: true,
          message: '请输入职位薪资描述'
        },
        limit: {
          required: true,
          message: '请输入招聘职位限制'
        },
        location: {
          required: true,
          message: '请输入工作地址'
        },
        classification: {
          required: true,
          message: '请选择职位分类'
        },
        details: {
          require: true,
          message: '请输入职位详情'
        }
      }
    }
  },
  methods: {
    querySearch: function(queryString, cb) {
      this.placeSearch.search(queryString, (status, result) => {
        if(status == 'complete') {
          let resultList = result.poiList.pois;
          for(let i in resultList) {
            resultList[i].value = resultList[i].name;
          }
          cb(resultList)
        }
      });
    },
    handleSelect: function(item) {
      this.placeSearch.getDetails(item.id, (status, result) => {
        if(status == 'complete') {
          let loc = result.poiList.pois[0];
          this.form.location = loc.cityname + loc.adname + loc.address + " " + loc.name
        }
      });
    },
    submit: async function() {
      this.$refs['form'].validate(async (valid) => {
        if (valid) {
          const params = {
            unifiedId: localStorage.getItem('unifiedId'),
            jobName: this.form.title,
            positionType: this.form.classification,
            reward: this.form.salary,
            workPlace: this.form.location,
            description: this.form.details
          };
          const resp = await addPosition(params);
          if (resp.status == 200 && resp.data.code == 'success') {
            this.$message.success('发布成功!');
            this.$router.push('/home');
          }
          else this.$message.error('发布失败!');
        } else {
          this.$message.warning('请完整填写职位信息!');
          return false
        }
      })
    }
  }
}
</script>

<style scoped>

</style>
