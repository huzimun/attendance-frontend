<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="员工id" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="employeeId">
              <a-input v-model="model.employeeId" placeholder="请输入员工id"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="是否打卡" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="isClockIn">
              <a-input-number v-model="model.isClockIn" placeholder="请输入是否打卡" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="是否完成考勤" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="isAttendance">
              <a-input-number v-model="model.isAttendance" placeholder="请输入是否完成考勤" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="打卡时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="clockInTime">
              <j-date placeholder="请选择打卡时间"  v-model="model.clockInTime" :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="系统完成考勤时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="attendanceTime">
              <j-date placeholder="请选择系统完成考勤时间"  v-model="model.attendanceTime" :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" style="width: 100%" />
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@api/manage'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: 'AttendanceInfoForm',
    components: {
    },
    props: {
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        model:{
         },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
           isClockIn: [
              { required: false},
              { pattern: /^-?\d+\.?\d*$/, message: '请输入数字!'},
           ],
           isAttendance: [
              { required: false},
              { pattern: /^-?\d+\.?\d*$/, message: '请输入数字!'},
           ],
        },
        url: {
          add: "/demo/attendanceInfo/add",
          edit: "/demo/attendanceInfo/edit",
          queryById: "/demo/attendanceInfo/queryById"
        }
      }
    },
    computed: {
      formDisabled(){
        return this.disabled
      },
    },
    created () {
       //备份model原始值
      this.modelDefault = JSON.parse(JSON.stringify(this.model));
    },
    methods: {
      add () {
        this.edit(this.modelDefault);
      },
      edit (record) {
        this.model = Object.assign({}, record);
        this.visible = true;
      },
      submitForm () {
        const that = this;
        // 触发表单验证
        this.$refs.form.validate(valid => {
          if (valid) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            httpAction(httpurl,this.model,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
            })
          }

        })
      },
    }
  }
</script>